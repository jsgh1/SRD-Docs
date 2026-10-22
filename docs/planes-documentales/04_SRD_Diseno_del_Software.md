# Plan de revisión del diseño del software

**Original:** [04_SRD_Diseno_del_Software.docx](../entregables-originales/04_SRD_Diseno_del_Software.docx). **Apoyo:** [texto de diseño](../sources/04_SRD_Diseno_del_Software.txt).

## Cambios de diseño a documentar

1. Contrastar cada vista y diagrama con el código y [ARQUITECTURA.md](../ARQUITECTURA.md). Identificar qué es diseño objetivo y qué está desplegado en local. El documento contempla infraestructura productiva en 443, WSS y TURN; no presentarla como configuración ya publicada. Señalar que Reportes especializado no está implementado como dominio independiente.
2. Actualizar pantallas y navegación web según [REVISION_UX_WEB.md](../REVISION_UX_WEB.md): Public Sans Variable, responsive, tarjetas móviles, confirmaciones internas, estados vacío/carga/error y reintentos. Redibujar capturas que hayan cambiado; no mezclar imágenes de la versión antigua con controles nuevos sin pie de versión.
3. Diseñar persistencia de idioma por usuario y textos ES/EN redactados por junta. Indicar fallback explícito para registros históricos que no tengan versión inglesa. Ubicar los campos y flujos afectados en modelo de datos, contratos, formularios, avisos y exportaciones. Distinguir identificadores internos de rótulos traducibles.
4. Actualizar secuencias de Chat, adjuntos, voz y llamadas con [CHAT.md](../CHAT.md), [NOTIFICACIONES.md](../NOTIFICACIONES.md) y [ARCHIVOS-PRIVADOS.md](../ARCHIVOS-PRIVADOS.md): borrado de mensaje propio/ajeno, visor, fijados, grabación/pausa/vista previa, llamada entrante/perdida y permisos; representar límites de red y antivirus.
5. Revisar los contratos y tablas de Personas, Calendario, Tesorería, Inventario, Carpetas, Auditoría, privacidad y outbox con los documentos de módulo. No inferir esquema solo de una captura: comprobar migraciones y contrato vigente. Registrar decisiones de compatibilidad para datos antiguos y clientes móviles.
6. Dibujar explícitamente los estados de error y recuperación: 503, timeout de lectura, resultado de escritura incierto, 409, 403 y servicio auxiliar no disponible. Una pantalla vacía por error no debe confundirse con ausencia real de datos.

## Revisión final para Chat

Mantener trazabilidad con SRS y Análisis. Actualizar índice de figuras/tablas, numeración, referencias, leyendas y control de versión. Marcar como **pendiente de prueba** TURN operativo, recuperación completa y las combinaciones web aún no aceptadas; los diagramas no son evidencia de ejecución.
