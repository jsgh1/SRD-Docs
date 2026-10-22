# Plan de revisión del análisis del software

**Original:** [02_SRD_Analisis_del_Software.docx](../entregables-originales/02_SRD_Analisis_del_Software.docx). **Apoyo:** [texto de análisis](../sources/02_SRD_Analisis_del_Software.txt).

## Actualizaciones a integrar

1. En el modelo de actores y casos de uso, contrastar los seis roles (Superadministrador, Administrador, Registrador, Tesorero, Auditor y Consultor) y sus permisos reales con [PERMISOS.md](../PERMISOS.md). Separar navegación/lectura ya probada de operaciones de escritura aún pendientes de matriz integral.
2. Revisar los flujos de autenticación, invitación, MFA, cambio de junta y aceptación de términos. Mostrar decisiones, errores, reintentos y salidas; no convertir una cuenta de persona registrada en cuenta de acceso automáticamente.
3. Agregar escenarios de análisis para idioma persistente y contenido redactado por junta ES/EN, incluidas versiones históricas sin inglés. El cambio fue solicitado después de la línea base y debe relacionarse con la propuesta de cambio del SRS.
4. Incorporar escenarios alternos concretos: lectura lenta, 503, resultado previo que no debe quedar como vigente; escritura cuyo resultado no se conoce, conflicto 409, permiso 403; antivirus no apto; caída temporal de servicio; restauración y reaplicación de supresiones. Indicar qué subsistema resuelve cada transición.
5. Revisar casos de Chat y llamadas contra [CHAT.md](../CHAT.md) y [NOTIFICACIONES.md](../NOTIFICACIONES.md). Distinguir estados y resultados persistentes de presencia momentánea; una llamada perdida o finalizada debe poder consultarse como evento del hilo según el alcance implementado.
6. Actualizar la matriz caso de uso → requisito → prueba con [TRAZABILIDAD.md](../TRAZABILIDAD.md). Usar «implementado parcialmente», «verificado en recorrido dirigido» y «pendiente de aceptación» con significado distinto.

## Entrega esperada

Integrar los cambios en diagramas, narrativas y escenarios que correspondan, revisar numeración y leyendas, y señalar brechas observadas sin presentar pruebas por hacer como aprobadas. El corte 19/10/2026 registra 33/140 archivos de navegador aprobados sobre una imagen; no equivale al cierre de los 20 casos de uso.
