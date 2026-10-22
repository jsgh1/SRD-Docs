# Plan de revisión de la propuesta técnica

**Original:** [03_SRD_Propuesta_Tecnica.docx](../entregables-originales/03_SRD_Propuesta_Tecnica.docx). **Apoyo:** [texto de la propuesta](../sources/03_SRD_Propuesta_Tecnica%281%29.txt).

## Qué actualizar

1. Separar con claridad **arquitectura propuesta** de **prototipo implementado**. La propuesta plantea 11 microservicios; la evidencia actual identifica un gateway y diez dominios presentes, mientras Reportes especializado sigue proyectado. Mantener la visión del SRS, pero no afirmar que todos los componentes planeados ya operan.
2. Actualizar la descripción de web React/TypeScript/Vite, backend Laravel/MySQL, Compose, correo local y clientes complementarios Windows/Android conforme a [ARQUITECTURA.md](../ARQUITECTURA.md), [MOVIL.md](../MOVIL.md) y [ESCRITORIO.md](../ESCRITORIO.md). Las bases son por **dominio**, no una por junta; el aislamiento por junta se aplica en contexto y operaciones.
3. Revisar fases, entregables y cronograma a partir del avance actual. Las capacidades web demostrables no equivalen a aceptación del 100 %. Mantener pendientes de idiomas ES/EN de extremo a extremo, revisión visual, escrituras por rol, regresión completa, adjuntos, restauración, TURN y TLS de producción.
4. Actualizar matriz de riesgos y mitigaciones: firmas antivirus vencidas con rechazo seguro de cargas; presión de memoria y reinicio de Docker en regresión extensa; dependencia de correo/MFA y planificadores; migraciones y respaldo; diferencias entre ambiente local y despliegue autorizado. No proponer saltar antivirus ni publicar servicios como «solución rápida».
5. Verificar presupuesto, recursos y alternativas tecnológicas: no inventar precios actuales ni ahorros medidos. Si se requieren cifras actuales, documentar fuente y fecha aparte. Conservar la distinción entre propuesta formativa, prototipo probado y piloto futuro.
6. Reescribir criterios de aceptación para que apunten a RF/RNF/CU, evidencia, responsable y condición de cierre. Los 33/140 archivos aprobados son un dato **fechado**, no porcentaje de completitud.

**Fuentes:** [TRAZABILIDAD.md](../TRAZABILIDAD.md), [WEB_ACEPTACION.md](../WEB_ACEPTACION.md), [PENDIENTES-WEB-2026-10-16.md](../PENDIENTES-WEB-2026-10-16.md), [REVISION_UX_WEB.md](../REVISION_UX_WEB.md). Mantener tablas y figuras legibles; actualizar sus títulos, pies y referencias cruzadas después de reestructurar.
