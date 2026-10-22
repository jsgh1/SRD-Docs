# Plan de revisión del SRS

**Original:** [01_SRD_Especificacion_de_Requisitos_SRS (1).docx](../entregables-originales/01_SRD_Especificacion_de_Requisitos_SRS%20%281%29.docx). **Apoyo de búsqueda:** [texto SRS](../sources/01_SRD_Especificacion_de_Requisitos_SRS.txt). **Naturaleza:** línea base de requisitos, no informe de avance.

## Qué debe hacer Chat

1. Conservar los identificadores y la intención de los **56 RF, 18 RNF y 20 CU**. No cambiar un criterio de aceptación porque la implementación aún sea parcial. Revisar control de versión, índice, glosario, referencias internas y trazabilidad antes de proponer una nueva versión.
2. Registrar como **solicitud de cambio posterior al SRS** el idioma inglés persistente por usuario. El RNF-011 vigente pide una interfaz en español; no describe el alcance bilingüe. Proponer nueva redacción o requisito complementario con criterios verificables: selector en Perfil; persistencia al recargar, cerrar y reabrir sesión; interfaz, errores, correos, avisos y exportaciones ES/EN; textos configurables guardados por junta en dos versiones redactadas por personas; datos históricos sin inglés visibles con su texto original hasta revisión humana. No prometer traducción automática.
3. Revisar si las correcciones posteriores de Chat y notificaciones ya están cubiertas por RF-041 a RF-046; donde falte precisión, proponer criterios de aceptación adicionales para borrar mensaje propio para ambas partes, ocultar recibido solo para uno, visor de imagen, fijados, nota de voz con pausa y vista previa, estados de escritura/grabación/llamada, historial de llamadas y lectura/descartado de avisos. No duplicar requisitos existentes.
4. Para archivos, privacidad y operación, conservar los criterios de análisis antivirus, supresión al restaurar, auditoría y recuperación. Aclarar condiciones de prueba y dependencias externas sin rebajar los criterios por bloqueos locales.
5. Añadir una tabla de **cambios propuestos y justificación**, distinguiendo requisito nuevo, aclaración y requisito intacto. Solo marcar una versión nueva como aprobada si el dueño académico del alcance acepta el cambio; hasta entonces etiquetarla como propuesta.

## Evidencia y límites

Comparar con [TRAZABILIDAD.md](../TRAZABILIDAD.md), [WEB_ACEPTACION.md](../WEB_ACEPTACION.md), [PENDIENTES-WEB-2026-10-16.md](../PENDIENTES-WEB-2026-10-16.md) y [CHAT.md](../CHAT.md). La documentación de avance **no sustituye** requisitos. Evitar porcentajes arbitrarios y la frase «web terminada» mientras no se cierre la aceptación. Respetar los datos y diagramas originales y no copiar información de la base de datos de ejemplo.
