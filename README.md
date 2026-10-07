# Aplicativo SIG-Logístico — LogiTech Solutions S.A.

Versión: v1.0.0 — Fecha: 2026-10-07

Aplicativo web para resolver la actividad "Estrategas de la Cadena de Suministro: Decisiones Inteligentes con un SIG-Logístico". Funciona por completo en el navegador: no requiere cuenta, login ni servidor, y los datos no salen del equipo de quien lo usa.

## Módulos
1. **Datos** — carga del CSV del formulario SIG o del template (CSV/XLSX), captura manual con ayudas, máscaras y validaciones.
2. **Diagnóstico** — lectura por rol (Director/a, Inventarios, Transporte, Compras, Servicio), hallazgos de calidad de datos y brechas de información.
3. **Escenarios** — ESC-01 bloqueo vial y combustible, ESC-02 efecto látigo, ESC-03 quiebra de proveedor; editables y combinables.
4. **Estrategias** — siete palancas con parámetros, costo, rol responsable y Δ IH.
5. **Dashboard** — IH de Base / Disrupción / Con estrategia, recuperación, costo de la homeostasis, scorecard, 10 gráficos, vista por rol, matriz de decisiones y sinergias. Cada cifra tiene "ver cálculo".
6. **Supuestos** — parámetros del motor, pesos y bandas del Índice de Homeostasis.
7. **Exportar** — checklist de la actividad, CSV (template), XLSX, JSON, PNG, vista imprimible y link para compartir.

Valores de control del caso demo: IH base 67,0 (Desequilibrio, KPI más débil: lead time); con los tres escenarios 46,5; con las siete estrategias 70,9 y recuperación de 119%.

## Uso
1. Abrir el link. Para ver el caso de ejemplo: botón "Cargar caso demo" (se carga solo la primera vez).
2. Cargar el CSV del SIG (formulario de captura o template) o capturar los datos.
3. Activar escenarios y estrategias, revisar el dashboard y exportar.
4. "Copiar link de este análisis" genera un link que reproduce el análisis exacto.

## Publicar en Azure Static Web Apps (plan Free)
1. Crear un repositorio en GitHub y subir `index.html` y `staticwebapp.config.json` a la raíz.
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
Abrir `index.html` con doble clic. Necesita internet solo para descargar las librerías (Chart.js, SheetJS, PapaParse, lz-string) desde cdnjs; sin ellas el cálculo funciona, pero no los gráficos ni las exportaciones XLSX.

## Actualizar
Reemplazar `index.html`, subir la versión en el pie (constante `VERSION`) y en este README, hacer push. Los links compartidos (`#estado=`) de versiones anteriores siguen abriendo mientras no cambie el esquema del estado.
