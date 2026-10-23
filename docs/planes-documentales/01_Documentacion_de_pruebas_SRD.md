# Plan de actualización de Documentación de pruebas

**Originales intactos:** [Word](../entregables-originales/01_Documentacion_de_pruebas_SRD.docx) · [PDF](../entregables-originales/01_Documentacion_de_pruebas_SRD.pdf). **Corte del original:** 9 de octubre de 2026. Sus secciones actuales son criterio de evaluación, entorno, inventario de evidencia, casos funcionales, correcciones, ejecución, incidencias y fuentes.

## Información nueva que Chat debe integrar

1. Actualizar el inventario de evidencias por **tipo, fecha, versión de imagen, módulo, rol y fuente reproducible**. [VALIDACION.md](../VALIDACION.md) registra pruebas de servicio y operación; [REVISION_UX_WEB.md](../REVISION_UX_WEB.md) registra lotes de navegador y hallazgos. Evitar sumar aserciones, casos y archivos como si fueran la misma unidad.
2. Crear o ampliar la matriz RF/RNF/CU → caso → precondición → datos ficticios → pasos → esperado → obtenido → evidencia → estado. Tomar identificadores de [TRAZABILIDAD.md](../TRAZABILIDAD.md). Indicar que «parcial» significa código con evidencia incompleta, no inexistencia. Marcar «bloqueado» cuando el entorno impide ejecutar.
3. Documentar con fecha el resultado **33 de 140 archivos distintos de navegador aprobados sobre una misma imagen web al 19/10/2026**. Los tres archivos cuyo primer intento falló por expectativas obsoletas aprobaron individualmente tras actualizar la prueba. No escribir «33/140 pruebas funcionales aprobadas» ni una tasa de éxito de la suite: quedan archivos sin ejecutar y los archivos tienen números distintos de casos.
4. Detallar las nuevas pruebas de estado vacío de 14 secciones a 320/1280 px, carga lenta, consulta exacta de Personas con 503 y reintento, navegación/permisos por seis roles, invitaciones, Chat, calendario, notificaciones y exportación de Auditoría. Citar el nombre real del `.spec.mjs` y el informe en `.local/browser-runs` cuando exista. No copiar credenciales ni datos de fixture.
5. Reescribir el procedimiento de ejecución: `scripts/Test-Browser.ps1 -Compose -Spec nombre.spec.mjs`, o `-Specs @('uno.spec.mjs','dos.spec.mjs')` para lotes con fixture separada por archivo. Añadir preparación, servicios requeridos, registro de imagen, limpieza y repetición después de corregir una falla.
6. Dejar abiertos los **tres criterios** de revisión visual integral, matriz de formularios/escrituras por rol y regresión de 140 archivos sobre la imagen final. La carga de adjuntos sigue bloqueada por firmas antivirus vencidas; no contarla como aprobada ni recomendar desactivar el análisis. Añadir plan de cierre con responsable, fecha real y evidencia exigida.

## Regla editorial

Conservar los resultados válidos del 9 de octubre como históricos y explicar cómo los nuevos resultados los amplían. Usar tablas para casos comparables y texto para interpretar límites. No presentar salud de contenedores como prueba de negocio ni documentación nueva como certificación de producción.
