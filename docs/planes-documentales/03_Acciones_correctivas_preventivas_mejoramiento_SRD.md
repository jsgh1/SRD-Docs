# Plan de actualización de Acciones correctivas preventivas y de mejoramiento

**Originales:** [Word](../entregables-originales/03_Acciones_correctivas_preventivas_mejoramiento_SRD.docx) · [PDF](../entregables-originales/03_Acciones_correctivas_preventivas_mejoramiento_SRD.pdf). El registro del 9 de octubre conserva valor histórico; no borrar acciones previas ni declararlas cerradas sin evidencia.

## Estructura que debe usar Chat

Para cada acción incluir **ID, tipo (correctiva/preventiva/mejora), fecha, hallazgo y evidencia, impacto, causa confirmada o hipótesis, contención, acción, responsable real o por asignar, plazo acordado o por definir, prueba de eficacia, estado y cierre**. No inventar responsables nominales ni fechas límite. Separar «corrección aplicada» de «eficacia comprobada» y «aceptación integral».

## Registros nuevos o actualizados

| Tema | Lo comprobado | Acción o verificación pendiente |
| --- | --- | --- |
| Resultado anterior en Consultar Personas | Una lectura nueva/fallida podía dejar visible la ficha previa; se añadió limpieza del resultado y carga. La prueba con 503 y reintento aprobó. | Conservar evidencia del recorrido; revisar patrón similar en otras lecturas durante la matriz final. |
| Tres archivos de navegador con expectativas antiguas | `service-status`, `person-date-ranges` y `person-base-filters` se ajustaron a estado API/controles vigentes y aprobaron individualmente. | Mantener revisión de selectores tras cambios UI; ejecutar la suite restante sobre imagen final. No registrar estos fallos como defectos productivos sin prueba. |
| Firmas antivirus vencidas | Cargas protegidas responden 503; el rechazo evita aceptar archivos sin análisis. | Actualizar firmas mediante procedimiento autorizado, verificar salud/fecha, repetir adjuntos y guardar informe. **Abierto por entorno**. |
| Docker sin respuesta durante lote amplio | Se reinició el motor; la web volvió a HTTP 200 y 23 servicios quedaron saludables en ese corte. | Plan de lotes pequeños, seguimiento de memoria y recuperación documentada; completar regresión. |
| UX y formularios | Hay siete criterios de diseño cerrados y tres abiertos. | Matriz visual de estados, temas y anchos; escrituras y rechazos por rol; 140 archivos en una imagen final. |
| Idioma persistente ES/EN | Existen traducciones y datos configurables bilingües en varios módulos. | Aceptación de extremo a extremo de errores, correos, avisos, PDF/XLSX y datos históricos con dos cuentas. |

## Fuentes y cautelas

Contrastar cada fila con [REVISION_UX_WEB.md](../REVISION_UX_WEB.md), [PENDIENTES-WEB-2026-10-16.md](../PENDIENTES-WEB-2026-10-16.md), [VALIDACION.md](../VALIDACION.md) y [WEB_ACEPTACION.md](../WEB_ACEPTACION.md). Añadir una revisión periódica y evidencia de eficacia por acción. No convertir los 33/140 archivos aprobados en un porcentaje de proyecto ni marcar cerrada la acción de antivirus o regresión.
