# Aplicativo SIG-Logístico — LogiTech Solutions S.A.

Versión: v1.6.0 — Fecha: 2026-10-07

Publicado en: https://aihyperplane.github.io/sig-logistico/

Aplicativo web para resolver la actividad "Estrategas de la Cadena de Suministro: Decisiones Inteligentes con un SIG-Logístico". Funciona por completo en el navegador: no requiere cuenta, login ni servidor, y los datos no salen del equipo de quien lo usa.

## Módulos
1. **Datos** — carga del CSV del formulario SIG o del template (CSV/XLSX), captura manual con ayudas, máscaras y validaciones.
2. **Diagnóstico** — lectura por rol (Director/a, Inventarios, Transporte, Compras, Servicio), hallazgos de calidad de datos y brechas de información.
3. **Escenarios** — ESC-01 bloqueo vial y combustible, ESC-02 efecto látigo, ESC-03 quiebra de proveedor; editables y combinables.
4. **Estrategias** — siete palancas de contingencia y un plan estructural, con parámetros, costo, rol responsable, Δ IH y optimizador de combinaciones.
5. **Dashboard** — IH de Base / Disrupción / Con estrategia, recuperación, costo de la homeostasis, scorecard, 10 gráficos, vista por rol, matriz de decisiones y sinergias. Cada cifra tiene "ver cálculo".
6. **Supuestos** — parámetros del motor, pesos y bandas del Índice de Homeostasis, y análisis de sensibilidad.
7. **Exportar** — checklist de la actividad, CSV (template), XLSX, JSON, PNG, vista imprimible y link para compartir.

### Novedades v1.6.0
- **Identificación del grupo** (estudiante(s), profesor/a y número de grupo) en la bienvenida y en Datos; aparece en CSV (sección 0, se relee al cargar), XLSX (hoja Identificación), JSON, borrador del informe, vista imprimible, nombres de archivo y comparación de entregas. El docente puede fijar su nombre en el link de la clase. Template de referencia con sección 0.
- **Mapa de la red y disrupciones** (módulo Escenarios): esquema generado con rutas, proveedores y matriz de escenarios; rutas por OTIF, marcadores por evento activo/inactivo/cualitativo, descarga PNG e inclusión en el borrador del informe.
- Corrección: "PNG de todos los gráficos" abre la subpestaña Gráficos antes de descargar.

### v1.5.1
- Corrección: en el celular, la ventana de bienvenida se desplaza y el botón Empezar queda siempre visible; las demás ventanas se ajustan a la altura de la pantalla.

### Novedades v1.5.0
- **Página de inicio** (`index.html`) con propósito, cómo funciona, el caso, los roles, el Índice de Homeostasis animado y la sección para docentes. El simulador pasa a `simulador.html`; los links `#estado=` y `#clase=` que lleguen a `index.html` se redirigen solos al simulador.
- **Nuevo logo** ("Ruta en S") y paleta azul petróleo / verde azulado / ámbar.
- **Navegación por fases** (Preparar, Simular, Evaluar): barra lateral en computador, fila horizontal en tableta y barra inferior de fases en celular.
- **Índice de Homeostasis siempre visible** en el encabezado y **menú Herramientas** con las funciones secundarias.
- **Bienvenida** al primer ingreso (estudiante/docente, rol, modo y datos) y **modo guiado / experto**, que el docente puede fijar en el link de la clase.
- **Datos en acordeón** con el estado de validación de cada sección.

### v1.4.1
- Corrección: botones de descarga del módulo Exportar se ajustan al ancho del celular.

### Novedades v1.4.0
- **Preguntas de reflexión** por módulo, **autoevaluación de conceptos** (12 preguntas) y **rúbrica integrada** con nivel sugerido y autoevaluación.
- **Borrador automático del Informe Ejecutivo** en Word (.docx) o HTML, con cifras, tablas y gráficos del análisis y espacios para la interpretación del comité.
- **Modo docente**: comparación de los JSON de los grupos con verificación de integridad, gráfico costo vs. IH y exportación XLSX; **configuración de la clase** por link (`#clase=`) que fija supuestos, pesos, escenarios y datos.
- **Subpestañas** en Estrategias y Dashboard, **planes A/B**, **deshacer/rehacer** (Ctrl/Cmd+Z).
- **Accesibilidad**: modo Accesible (patrones en gráficos), "Ver datos del gráfico", tablas como tarjetas en celular.
- **Versión sin internet**: `SIG_Logistico_sin_internet.html` (librerías incluidas, un solo archivo).

### Novedades v1.3.0
- **Bitácora de decisiones**: registra cada cambio con IH antes/después y justificación; alimenta la Matriz de Decisiones y se exporta (hoja Bitácora).
- **Predecir antes de simular**: al activar escenarios o estrategias se pide una predicción y se compara con el resultado.
- **Variantes del caso por grupo**: un código genera un caso reproducible de dificultad equivalente.
- **Barra de avance** del ejercicio y navegación entre módulos; **diseño móvil** con menú y pestañas inferiores.

### Novedades v1.2.0
- Secciones 1 (KPIs) y 6 (matriz de escenarios) editables: agregar, modificar y eliminar registros; indicadores adicionales informativos; modelo de simulación por evento (ESC-01/02/03 o cualitativo).
- Listas estandarizadas (picklists) con opción "Otro…" que amplía el catálogo.
- Template de referencia XLSX (listas desplegables, instrucciones y diccionario de campos) y CSV, descargables desde el módulo Datos.
- Manual de usuario (`manual.html` y `Manual_SIG_Logistico.pdf`) con el paso a paso y la guía del ejercicio académico.
- Aviso de propiedad intelectual de AI HYPERPLANE S.A.S. y términos de uso académico.

### Novedades v1.1.0
- **Sección 8 opcional de costos** (transporte, fletes urgentes, almacenamiento, mantenimiento de flota, administración, costo de capital, costo de ventas, penalidad por pedido): reemplaza supuestos por valores derivados, calcula el costo de transporte por unidad y valora fletes urgentes y penalidades. Botón de valores ilustrativos (marcados como supuesto).
- **Optimizador**: evalúa las 256 combinaciones de las 8 palancas y muestra la frontera eficiente costo–IH.
- **Análisis de sensibilidad** (±10/20/30%) con gráfico tornado y veredicto de robustez.
- **Plan estructural de compras** (nueva palanca de mediano plazo) y comparación contingencia vs. estructural.
- **Ayudas y guía académica**: ayudas emergentes accesibles, recomendaciones por módulo, glosario y referencias.
- Motor 1.1.0: cambios documentados en `references/formulas.md` §9; los valores de control no cambian.

Valores de control del caso demo: IH base 67,0 (Desequilibrio, KPI más débil: lead time); con los tres escenarios 46,5; con las siete estrategias 70,9 y recuperación de 119%.

## Propiedad intelectual y uso
© 2026 AI HYPERPLANE S.A.S. Propiedad intelectual de AI HYPERPLANE S.A.S. El simulador puede usarse libremente con fines académicos y educativos, citando la fuente; cualquier otro uso requiere autorización escrita. Ver `TERMINOS_DE_USO.md`.

## Archivos del sitio
`index.html` (página de inicio) + carpeta `inicio/img/`, `simulador.html` (simulador), `SIG_Logistico_sin_internet.html` (simulador con librerías incluidas), `manual.html` + carpeta `manual/img/` (manual), `Manual_SIG_Logistico.pdf`, `SIG_Logistico_template_referencia.xlsx` y `.csv`, `staticwebapp.config.json`, `.nojekyll`.

## Uso
1. Abrir el link: la página de inicio explica el ejercicio; "Abrir el simulador" lleva a `simulador.html`. La primera vez aparece la bienvenida (perfil, rol, modo y datos).
2. Cargar el CSV del SIG (formulario de captura o template) o capturar los datos.
3. Activar escenarios y estrategias, revisar el dashboard y exportar.
4. "Copiar link de este análisis" genera un link que reproduce el análisis exacto.

## Publicar en Azure Static Web Apps (plan Free)
1. Crear un repositorio en GitHub y subir a la raíz `index.html`, `simulador.html`, las carpetas `inicio/` y `manual/` y `staticwebapp.config.json`.
2. En portal.azure.com: Crear recurso → Static Web App → plan Free → Origen: GitHub → seleccionar repo y rama `main`.
3. Detalles de compilación: Preset "Custom", App location `/`, Api location vacío, Output location vacío.
4. Crear. Azure agrega un flujo de GitHub Actions; al terminar, la URL aparece en la página del recurso (`https://<nombre>.azurestaticapps.net`).
5. Cada push a `main` publica la nueva versión. Dominio propio: Static Web App → Dominios personalizados.

Sin repositorio (CLI):
```
npm install -g @azure/static-web-apps-cli
swa deploy ./ --env production --deployment-token <TOKEN_DE_LA_STATIC_WEB_APP>
```
El token se obtiene en el recurso → "Administrar token de implementación". No lo guardes en el repositorio.

## Publicar en GitHub Pages
1. Repositorio público con `index.html` en la raíz.
2. Settings → Pages → Source: Deploy from a branch → `main` / `(root)` → Save.
3. URL: `https://<usuario>.github.io/<repositorio>/` (tarda uno o dos minutos la primera vez).

## Uso local
Abrir `simulador.html` con doble clic. Necesita internet solo para descargar las librerías (Chart.js, SheetJS, PapaParse, lz-string) desde cdnjs; sin ellas el cálculo funciona, pero no los gráficos ni las exportaciones XLSX. Sin conexión, usar `SIG_Logistico_sin_internet.html`.

## Actualizar
En el repositorio: Add file → Upload files → arrastrar los archivos nuevos (`index.html`, `simulador.html`, `SIG_Logistico_sin_internet.html`, `manual.html`, carpetas `inicio/` y `manual/`, PDF) → Commit. Subir la versión en el pie (constante `VERSION`) y en este README, hacer push. Los links compartidos (`#estado=`) de versiones anteriores siguen abriendo mientras no cambie el esquema del estado.
