# SRD P1 — validación funcional, seguridad y aislamiento multi-junta

Fecha: 27 de octubre de 2026
Rama: `feat/mobile-native-paridad-apk-20261027`
Entorno: Compose aislado `srd-mobile-showcase-20261027`, Gateway `http://localhost:8080`, Mailpit local `http://localhost:8025`, organizaciones sintéticas `srd-e2e-a` y `srd-e2e-b`. No se usó producción ni se modificaron datos reales.

## Resultado ejecutivo

| Prueba | Estado | Evidencia / alcance |
|---|---|---|
| Roles Backend y visibilidad móvil | PASS | Comparación de `SRD-Backend/packages/php/src/Access.php` con `SRD-Frontend/apps/mobile/App.tsx`; matriz API con seis roles. |
| Credenciales incorrectas, MFA y login Android | PASS | Credencial sintética incorrecta: 401. Login y OTP correctos en la UI del APK con `treasurer`; desafío consumido y dashboard de B visible. MFA previo de `admin` y `superadmin` validado por API. |
| Throttling en reintentos MFA | BLOCKED | Repeticiones de login de `admin` y `superadmin` recibieron 429. No se eludió el límite; la protección se mantuvo activa. |
| CSRF, cookie y sesión | PASS | Operaciones sin CSRF/cookie: 419; expiración: 401; logout UI/API y restauración de sesión comprobados. |
| Cambio de junta e aislamiento | PASS | A↔B desde APK/API; Personas, Agenda, Inventario y Tesorería de B no se enumeran en A; lecturas cruzadas por ID: 404. |
| Personas y Agenda en APK | PASS | Consulta, búsqueda, paginación, ficha, Atrás, eventos, detalle e invitaciones/aceptación. |
| Inventario en APK | PASS | Búsqueda/filtro, páginas 1 y 2 con tamaño real 25, detalle e historial; Atrás. Regresión de paginación corregida. |
| Tesorería en APK | PASS | Saldos, filtros/búsqueda, historial paginado, detalle y Atrás; lectura solamente. |
| Perfil, notificaciones y logout UI | PASS | Vistas cargadas desde la app y cierre visible con retorno a login. |
| Timeout, error y reintento de red | PASS | Denegación de red solo al paquete durante más de 15 s mostró el error; al restaurar red, «Actualizar» recuperó saldo e historial. |
| TypeScript | PASS | `npx tsc --noEmit`, sin errores. |
| Jest | PASS | 5 suites, 45/45 pruebas. Incluye límite de página y error/reintento. |
| ESLint | PASS | 0 errores; 3 advertencias preexistentes en `App.tsx` (`no-void` ×2 y `no-shadow` ×1). |
| PHPUnit Backend | BLOCKED | El contenedor no contiene `vendor/bin/phpunit`; PHP/Composer no están disponibles en el host. No se instalaron herramientas. |
| APK autónomo en emulador | PASS | APK debug firmado, instalado y abierto en Android 17/API 37, con bundle incluido y sin Metro. |
| Teléfono físico | NOT RUN | ADB solo expuso el emulador; no había teléfono físico conectado. |

## Roles y permisos

El Backend declara `superadmin`, `admin`, `registrar`, `treasurer`, `auditor` y `viewer`. La app oculta Inventario y Tesorería salvo para `superadmin`, `admin` y `treasurer`; permite registrar Personas a `superadmin`, `admin` y `registrar`. Las rutas protegidas vuelven a comprobar el rol en Backend, aunque la pantalla móvil no aparezca.

| Rol | Lecturas comunes | Inventario / Tesorería | Escribir Personas / invitar | Auditoría | Plataforma | Resultado de API |
|---|---|---|---|---|---|---|
| `superadmin` | permitido | permitido | permitido | permitido; reintento según ACL | permitido | PASS en la validación autorizada inicial; repetición MFA limitada por 429. |
| `admin` | permitido | permitido | permitido | permitido | denegado | PASS; escrituras vacías alcanzaron validación (422), las lecturas protegidas coincidieron con ACL. |
| `registrar` | permitido | denegado (403) | permitido | denegado (403) | denegado | PASS; escritura de Persona/invitación alcanzó validación (422). |
| `treasurer` | permitido | permitido | denegado (403) | denegado (403) | denegado | PASS; escrituras de Inventario/Tesorería alcanzaron validación (422). |
| `auditor` | permitido | denegado (403) | denegado (403) | lectura/exportación permitidas | denegado | PASS. |
| `viewer` | permitido | denegado (403) | denegado (403) | denegado (403) | denegado | PASS; además, creación de evento con cuerpo completo devolvió 403 directamente. |

Para las rutas donde Backend autoriza antes de validar, `422` con cuerpo vacío significa que el rol alcanzó validación y no creó datos; `403` significa que la autorización rechazó la operación. No se tomó un `422` de una ruta que valida antes de autorizar como prueba de denegación. Se hizo una llamada completa de creación de evento con `viewer`, que recibió `403`. La escritura de calendario depende también de `editor_roles` delegado por junta; la interfaz no sustituye la ACL del servicio.

Los GET directos a Inventario/Tesorería sin sesión devolvieron 401. El cambio de junta por `viewer` devolvió 403; cambios autorizados A↔B devolvieron 200. Las comprobaciones directas se hicieron con cookies/CSRF válidos y usuarios sintéticos para separar autorización de errores de transporte.

## Login, MFA y sesión

- La prueba con contraseña sintética incorrecta recibió 401. El endpoint usa una respuesta genérica para credencial, usuario o membresía inválidos; el status por sí solo no permite distinguir cuál de esos casos es.
- En un intento manual temprano, la pantalla conservaba caracteres adicionales en el campo de contraseña y mostró el mensaje genérico de login. Se limpió el campo, se reingresó la credencial sintética rotada y Gateway emitió el desafío MFA; el OTP se leyó de Mailpit aislado, se ingresó en Android y el desafío quedó consumido. El APK mostró el dashboard de la junta B. No se guardó ni imprimió la contraseña o el OTP.
- El MFA de `admin` y `superadmin` tuvo autenticaciones API exitosas durante la matriz inicial; reintentos posteriores recibieron 429 por throttling. El resultado se conserva como bloqueo de repetición, no como fallo de credenciales.
- POST sin cabecera CSRF y solicitud protegida sin cookie de sesión: 419. El login nuevo usa el flujo Gateway y la app autenticada conserva la cookie requerida.
- Restauración de sesión: el proceso Android se forzó a detener y reabrir; `/me` mantuvo identidad y junta B.
- Expiración: una sesión sintética envejecida más de 30 minutos respondió 401 en `/me`.
- Cierre: el botón de la UI cerró la sesión y volvió a login; por API, `/logout` respondió 200 y el `/me` posterior 401.
- La contraseña sintética expuesta en una salida de diagnóstico de P0 se rotó antes de la ejecución. La contraseña vigente coincide con el hash guardado para el fixture de treasurer; fixture, OTP y valores secretos no están en los cambios ni en capturas versionadas.

## Aislamiento multi-junta

Se crearon registros identificables solo como datos sintéticos de prueba en `srd-e2e-b`: Personas, eventos/invitaciones, bienes e historial, y movimientos de Tesorería. En B se encontraron por búsqueda; en A, las listas no retornaron sus marcadores y las lecturas directas por ID cruzado devolvieron 404. El cambio A↔B, la vuelta a A y la restauración de sesión B también se observaron desde la UI Android. La prueba de permisos no usó endpoints inventados ni datos de producción.

## Recorrido Android y evidencia

Las capturas están en [`Entregables/Evidencias`](Evidencias). Se tomaron del emulador conectado al Compose aislado; no contienen credenciales ni OTP.

- Login/MFA concluido y dashboard B: [dashboard B final](Evidencias/mobile-p1-dashboard-b-final.png); restauración: [sesión restaurada](Evidencias/mobile-p1-session-restored.png); cambio de junta: [dashboard A](Evidencias/mobile-p1-dashboard-a.png).
- Personas: [lista](Evidencias/mobile-p1-people-page-1.png), [detalle](Evidencias/mobile-p1-person-detail.png).
- Agenda: [agenda](Evidencias/mobile-p1-agenda.png), [evento](Evidencias/mobile-p1-event-detail.png), [invitaciones](Evidencias/mobile-p1-invitations.png), [invitación aceptada](Evidencias/mobile-p1-invitation-accepted.png).
- Inventario en APK final: [página 1](Evidencias/mobile-p1-inventory-page-1-final.png), [página 2](Evidencias/mobile-p1-inventory-page-2-final.png), [ficha e historial](Evidencias/mobile-p1-inventory-detail-final.png).
- Tesorería: [saldos e historial](Evidencias/mobile-p1-treasury-final.png), [filtro](Evidencias/mobile-p1-treasury-filter-final.png), [paginación](Evidencias/mobile-p1-treasury-page-2-final.png), [detalle](Evidencias/mobile-p1-treasury-detail-final.png).
- Otras pantallas: [perfil](Evidencias/mobile-p1-profile-final.png), [notificaciones](Evidencias/mobile-p1-notifications-final.png), [logout UI](Evidencias/mobile-p1-logout-final.png).
- Red: [error tras timeout](Evidencias/mobile-p1-network-error-final.png), [respuesta después del reintento](Evidencias/mobile-p1-network-retry-final.png).

Los resultados detallados de pruebas y los datos de fixture permanecen fuera de los repositorios en `C:\srd-qa-20261027\.local`; solo se publican resumen, estados HTTP y capturas anonimizadas. Las salidas antiguas con `429` no se interpretan como credenciales erróneas.

## Regresiones y cambios de P1

1. **Paginación de Inventario.** La app suponía páginas de 10; `GET /api/v1/assets` devuelve `page_size=25`. La pantalla usa el tamaño de respuesta del Backend. La prueba Jest con 26 elementos exige que Siguiente esté habilitado en página 1 y deshabilitado en página 2. El APK final mostró la página 2 con 26 bienes y el botón Siguiente deshabilitado.
2. **Solicitudes que quedaban cargando sin conexión.** Se añadió timeout de 15 segundos mediante `AbortController`; cancelaciones se convierten en `NetworkError` y la pantalla muestra un mensaje recuperable. Jest prueba la conversión y el refresh. En Android, una regla de red de paquete bloqueó temporalmente solo `com.srd.mobile`; el error se vio en Tesorería y el mismo control recuperó los datos al restaurar acceso. La cadena de bloqueo quedó deshabilitada y el paquete en `allow`.

No se alteraron diseño, contratos API, roles, permisos ni datos; los cambios móviles solo consumen `page_size` ya devuelto y limitan el tiempo de solicitudes.

## APK final

- Versión instalada: `1.0` (`versionCode=1`), paquete `com.srd.mobile`, `targetSdk=36`.
- Ruta: `SRD-Frontend/apps/mobile/android/app/build/outputs/apk/debug/app-debug.apk`.
- Tamaño: 126,621,319 bytes; SHA-256: `FB90850BED650D078018CA7A1F6D60F3BC8059F383914CED8AA5E0A4DA89CB3E`.
- Firma verificada con APK Signature Scheme v2; bundle JS incluido en el APK.
- Instalación con ADB y apertura confirmadas en emulador Android 17/API 37. El puerto local de Metro 8081 no tenía listener durante la verificación.
- No se conectó un teléfono físico.

## Límites y seguimiento

- La suite PHPUnit del Backend quedó bloqueada por ausencia de dependencias en la imagen del contenedor. La matriz de Gateway, aislamiento y CSRF se ejecutó contra servicios Compose reales; PHPUnit no se declaró PASS.
- Los reintentos MFA de `admin` y `superadmin` quedaron bajo throttling 429 y no se repitieron ni eludieron. Las autenticaciones iniciales de ambos fueron aceptadas.
- Solo se probó en emulador Android 17/API 37; no hay resultado de dispositivo físico.
- No se fusionó ningún PR. Los cambios se publican en los PR existentes de Frontend #4 y Docs #4.
