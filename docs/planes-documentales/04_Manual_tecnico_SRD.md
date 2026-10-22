# Plan de actualización del Manual técnico

**Originales:** [Word](../entregables-originales/04_Manual_tecnico_SRD.docx) · [PDF](../entregables-originales/04_Manual_tecnico_SRD.pdf). Es un manual extenso; conservar sus diagramas, tablas, contratos y explicaciones aún vigentes. Actualizar en la sección propietaria, no mediante un resumen final.

## Qué debe revisar y ampliar Chat

1. **Topología y código:** contrastar `compose.yaml`, `services/`, `apps/` y [ARQUITECTURA.md](../ARQUITECTURA.md). Identificar gateway, diez dominios implementados, MySQL por dominio, planificadores y correo local. Distinguir Reportes especializado planeado de funciones de exportación existentes. Dar rutas y responsabilidades reales, sin atribuir a todos los dominios el mismo esquema interno si el código difiere.
2. **Seguridad:** explicar sesión/CSRF en la web, contexto firmado de usuario/junta/permiso, autorización secundaria en dominio, límites de descarga, archivos privados, análisis antivirus y riesgo de firmas vencidas. Aclarar que ocultar un botón no sustituye autorización API. No incluir secretos ni valores de `.env`.
3. **Persistencia y consistencia:** actualizar migraciones, versiones y conflictos 409, outbox, reintentos, deduplicación, limpieza durable de fotos, supresión y límites de restauración. Vincular [OUTBOX.md](../OUTBOX.md), [SUPRESIONES.md](../SUPRESIONES.md), [RESPALDOS.md](../RESPALDOS.md) y documentos de cada servicio.
4. **Interfaz y contratos:** incorporar React 19/TypeScript/Vite donde lo confirme `apps/web/package.json`, Public Sans Variable, preferencia de idioma persistente, datos configurables ES/EN por junta y fallback histórico. Describir tiempos máximos de lectura/escritura y recuperación tras 503 o espera excesiva; una escritura incierta se comprueba antes de repetir.
5. **Chat y tiempo real:** documentar señalización, permisos, adjuntos, borrado diferencial, audio y llamadas conforme a [CHAT.md](../CHAT.md). TURN entre redes, dispositivos reales y carga siguen sin aceptación completa. No afirmar disponibilidad productiva de videollamadas ni grabación.
6. **Operación y pruebas:** comandos efectivos de compilación, migración, salud, respaldo y `scripts/Test-Browser.ps1 -Specs`; ubicar informes y explicar 33/140 archivos de navegador como resultado parcial fechado. Registrar el incidente Docker y el bloqueo antivirus, con respuesta diagnóstica y sin atajos inseguros.

## Resultado esperado

Manual utilizable por otra persona para localizar código, comprender flujo de una petición, reproducir una prueba y diagnosticar una falla. Revisar todas las tablas de servicios, puertos, contratos y diagramas frente al repositorio actual; señalar como plan lo que aún no está implementado o no se ha aceptado. Referencias de control: [TRAZABILIDAD.md](../TRAZABILIDAD.md) y [WEB_ACEPTACION.md](../WEB_ACEPTACION.md).
