# Fórmulas del motor SIG-Logístico

Cada fórmula aquí corresponde a una función de `scripts/motor.js`. Si cambias una, cambia las dos y actualiza las pruebas.

## Contenido
1. Parámetros y supuestos
2. Diagnóstico base por rol
3. Simulación de stock
4. Escenarios de disrupción
5. Estrategias
6. KPIs globales por estado
7. Índice de Homeostasis
8. Cómo explicar una cifra

## 1. Parámetros y supuestos (`PARAMS_DEFAULT`)

| Parámetro | Defecto | Por qué existe |
|---|---|---|
| margen | 25% | El template no trae precios; precio proxy = costo × 1.25 |
| diasProteccion | 7 días | No hay desviación de la demanda para usar Z·σ·√LT |
| umbralSobrestockDIO | 45 días | Límite para clasificar sobrestock |
| horizonteDias | 30 | Los KPIs del template son mensuales |
| viajesMesPorRuta | 20 | El TMS no trae frecuencia de viajes |
| participacionCombustible | 40% | Para trasladar +20% de combustible al flete |
| tasaMantenerInventarioMes | 2% | Costo de capital y almacenamiento del inventario |
| participacionRutaAfectada | 1 / n.º de rutas troncales | Peso de la ruta afectada en el OTIF global |
| pesosIH | 20% cada KPI | Neutralidad entre roles |

Unidades por pedido = Σ demanda mensual / Σ pedidos totales (derivado, ≈1.42 en el caso).

## 2. Diagnóstico base por rol

**Inventarios (WMS), por SKU:** d = demanda / 30; cobertura = stock / d; DIO calculado = stock / demanda × 30; valor = stock × costo; lead time = promedio del lead time de los proveedores de su categoría; SS recomendado = d × diasProteccion; ROP recomendado = d × lead time + SS recomendado. Estado: "Quiebre inminente" si stock < SS; si no, "Reordenar" si stock < ROP; si no, "Sobrestock" si DIO > umbral; si no, "Sano". Hallazgos: DIO reportado difiere en más de 1 día del calculado; rotación reportada difiere más de 20% de 365 / DIO.

**Transporte (TMS), por ruta:** costo por km = flete / km; capacidad ociosa = 100 − ocupación; troncal = sale del CD.

**Proveedores:** riesgo = (lead time / meta de lead time) × (1 − fill rate / 100) × (10 / calidad). Ranking de mayor a menor riesgo.

**Servicio:** Perfect Order por canal = conformes / totales; global ponderado = Σ conformes / Σ totales; tasa de reclamos = reclamos / totales; costo inverso por pedido = costo inverso / totales.

**Global:** costo logístico mensual $ = ventas × costo % / 100.

## 3. Simulación de stock (`simularStock`)

Día a día durante el horizonte: suman las llegadas del día, se atiende min(stock, demanda diaria × multiplicador), lo no atendido se acumula como venta perdida. Devuelve la serie diaria, unidades no atendidas, stock promedio y día de quiebre (primer día con demanda no atendida).

Reposición estándar (supuesto): un pedido igual a la demanda mensual llega al cumplirse el lead time del proveedor. Cada estado se compara contra la simulación base del mismo SKU, de modo que solo cuenta el daño adicional.

## 4. Escenarios de disrupción (`ESCENARIOS_DEFAULT`)

**ESC-01 Transporte** (ruta RT-02, 5 días, +35% tiempo, +20% flete en desvío, +20% combustible):
- Combustible: Δ costo = costo mensual de todas las rutas × 20% × participación del combustible.
- Desvío: Δ costo = viajes × (días / 30) × flete de la ruta × 20%.
- OTIF de la ruta durante el bloqueo = OTIF base / 1.35; OTIF mensual = mezcla ponderada por días.
- Δ OTIF global = (OTIF mensual de la ruta − OTIF base) × participación de la ruta.

**ESC-02 Efecto látigo** (SKU-102, +80%): multiplicador de demanda 1.8 en la simulación del SKU. El quiebre, las unidades no atendidas y las ventas perdidas salen de la simulación.

**ESC-03 Quiebra de proveedor** (PRV-01, 21 días): los SKU de su categoría reciben su reposición 21 días más tarde (si cae fuera del horizonte, no llega). Lead time efectivo de la categoría = lead time + 21.

Los escenarios se combinan libremente; ESC-02 y ESC-03 sobre el mismo SKU se acumulan.

## 5. Estrategias (`ESTRATEGIAS_DEFAULT`)

| Estrategia | Rol | Efecto | Costo |
|---|---|---|---|
| e1_3pl | Transporte | OTIF de la ruta afectada durante el bloqueo = objetivo (defecto: su OTIF base) | Reemplaza el desvío: viajes × (días/30) × flete × 15% |
| e2_consolidacion | Transporte | Rutas con ocupación < 90% suben a 90%; viajes = viajes × ocupación / 90 | Ahorro = viajes eliminados × flete (incluye alza de combustible) |
| e3_ssrop | Inventarios | SKU con stock < ROP recomendado piden ROP rec − stock + demanda en su reposición | Capital adicional × 2% mensual |
| e4_rebalanceo | Inventarios | SKU en sobrestock bajan a DIO objetivo (30 días) | Ahorro = capital liberado × 2% mensual |
| e5_alterno | Compras | Pedido de emergencia al alterno por el faltante extra × fill rate, llega en su lead time (10 días) | Unidades × costo × 12% de sobrecosto |
| e6_sop | Director | Reduce el pico del efecto látigo en 50% | Sin costo directo (supuesto) |
| e7_calidad | Servicio | Devoluciones por daño −30%; esos pedidos pasan a perfectos | Ahorro = costo de logística inversa × 30% |

e5_alterno tiene `reemplazaProveedor` (defecto false): si es false, el lead time vuelve al del proveedor habitual; si es true, el comité decidió cambiar de proveedor y el lead time de la categoría pasa a ser el del alterno.

Regla conservadora: por SKU, Δ unidades no atendidas = máx(0, simulación del estado − simulación base). Ninguna estrategia genera ventas por encima de la base.

## 6. KPIs globales por estado

- Ventas del estado = ventas − Σ Δ no atendidas × costo × (1 + margen).
- Costo logístico % = (costo $ base + Σ Δ costos) / ventas del estado × 100.
- Pedidos afectados = Σ Δ no atendidas / unidades por pedido.
- OTIF = OTIF base + Δ OTIF de transporte − pedidos afectados / pedidos totales × 100 (acotado 0-100).
- Perfect Order = base − pedidos afectados / pedidos totales × 100 + pedidos recuperados por calidad / pedidos totales × 100.
- Rotación = base × (ventas del estado / ventas) × (inventario promedio valorizado base / del estado).
- Lead time = base × (lead time efectivo ponderado / lead time base ponderado), ponderando cada SKU por demanda × costo.
- Semáforo: verde si cumplimiento ≥ 1; amarillo si ≥ 0.9; rojo si menor.

## 7. Índice de Homeostasis (indicador propuesto por el comité)

No viene del documento de la actividad. Resume en una cifra el equilibrio de la cadena.

- Cumplimiento por KPI: mayor es mejor → mín(actual / meta, 1); menor es mejor → mín(meta / actual, 1).
- IH = 100 × Σ peso × cumplimiento. Pesos iguales por defecto; deben sumar 100% o el motor da error.
- Bandas: ≥ 90 Equilibrio; 75 a 89.9 Tensión; < 75 Desequilibrio.
- KPI más débil: menor cumplimiento. Se muestra siempre junto al IH.
- Tasa de recuperación = (IH estrategia − IH disrupción) / (IH base − IH disrupción) × 100. Si la caída es menor a 0.05 puntos: "no aplica". Más de 100% significa que la organización quedó mejor que antes del choque.
- Costo de la homeostasis = Σ costos atribuibles a las estrategias / puntos de IH recuperados.

Justificación para el informe: el tope en 1 impide que sobrecumplir un KPI compense el deterioro de otro, que es justamente la idea de equilibrio; los pesos iguales evitan sesgar hacia un rol; el IH depende de las metas de la empresa, por eso sirve para comparar estados del mismo caso, no empresas entre sí.

Caso de referencia: costo 0.70, OTIF 0.86, rotación 0.53, lead time 0.42, perfect order 0.85 → IH 67.0.

## 8. Cómo explicar una cifra

Cuando el usuario pregunte "¿de dónde sale X?": nombra la fórmula, sustituye los valores del caso, muestra el resultado y di qué supuesto interviene. Ejemplo: "El día de quiebre del SKU-102 con efecto látigo es el 6: demanda diaria 800/30 × 1.8 = 48 unidades; 250 unidades alcanzan para 5.2 días, así que el día 6 ya no se atiende toda la demanda."

## 9. Cambios v1.1 (decisión del comité, 2026-10-07)

Los valores de control con el template sin sección 8 no cambian (67,0 / 46,5 / 70,9 / 119% con las siete estrategias de contingencia).

**Sección 8 opcional — costos logísticos y financieros** (`COSTOS_DEF`): transporte regular, fletes urgentes, almacenamiento, mantenimiento de flota, administración y procesamiento de pedidos ($/mes), costo de capital del inventario (% anual), costo de ventas ($/mes) y penalidad por pedido incumplido ($/pedido). Cada fila lleva su fuente (Capturado o Ilustrativo).

**Parámetros derivados** (`derivarParametros`), que reemplazan al supuesto cuando el dato existe:
- margen = ventas / costo de ventas − 1.
- viajes por ruta = transporte regular / Σ flete por viaje.
- costo mensual de mantener inventario = costo de capital anual / 12 + almacenamiento × fracción variable (15%, supuesto) / valor del inventario.

**Desglose** (`desgloseCostos`): componentes ingresados + costo de capital + logística inversa de la sección 5; se compara con ventas × costo % reportado (si difieren, hallazgo de calidad de datos; no se sobrescribe el KPI). Cierra la brecha del costo de transporte por unidad = (transporte + fletes urgentes) / Σ demanda.

**Efectos nuevos** (solo si el costo fue ingresado):
- Consolidación: ahorro adicional = mantenimiento de flota × 50% (supuesto) × viajes eliminados / viajes totales.
- SS/ROP: reduce fletes urgentes 25%; S&OP: reduce 25% de lo que queda (supuestos editables).
- Penalidades = pedidos afectados × penalidad; se reportan en el impacto $, no en el KPI de costo logístico.

**e8_estructural — Plan estructural de compras** (Gestor/a de Compras): los proveedores con lead time mayor al objetivo (todos o uno elegido) pasan al lead time objetivo (10 d). Lead time efectivo = lead time efectivo − lead time habitual + objetivo (se conserva el retraso de la disrupción). Costo = 3% (supuesto) sobre las compras mensuales de esas categorías, contado como costo logístico (conservador). No modifica la simulación diaria: no se reclaman ventas por llegar antes. Es una palanca de mediano plazo; se presenta separada de las siete de contingencia.

Caso de referencia con las siete de contingencia + plan estructural: IH 79,8 (Tensión), lead time 5,4 días, KPI más débil pasa a ser el costo logístico.

## 10. Cambios v1.2 (lectura de datos)

- Sección 1: las filas que no corresponden a los cinco KPIs del IH se conservan como **indicadores adicionales** (`kpisExtra`), con columnas opcionales 6 (unidad) y 7 (mejor si: mayor/menor). Son informativos: no entran al Índice de Homeostasis.
- Sección 6: columna opcional 6 **Modelo de simulación** (ESC-01 transporte, ESC-02 demanda, ESC-03 proveedor o Cualitativo). Si falta, se infiere del ID (ESC-01/02/03); los demás eventos quedan como cualitativos: se documentan en la matriz y el informe, pero el simulador no los cuantifica.
- Ningún cálculo cambia; los valores de control se mantienen.
