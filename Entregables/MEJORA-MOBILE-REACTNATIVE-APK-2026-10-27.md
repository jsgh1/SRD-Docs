# SRD Mobile React Native: APK, autenticación y paridad funcional

**Fecha:** 27 de octubre de 2026
**Rama:** `feat/mobile-native-paridad-apk-20261027`
**Entorno:** Compose aislado `srd-mobile-showcase-20261027`; datos sintéticos.

## Resultado

Se validó desde la interfaz Android el acceso real al Gateway local con contraseña, MFA y carga del tablero. Personas, Agenda, detalle de eventos e invitaciones también se probaron desde el APK. Se agregó Inventario con listado, búsqueda, ficha e historial; Tesorería muestra saldos, movimientos, filtros y comprobantes en modo de solo lectura.

La autenticación quedó resuelta. El HTTP 401 observado en el primer intento se debió a una credencial sintética equivocada: se había usado el identificador del usuario en lugar del campo `password` del fixture. Con la contraseña sintética correcta, la misma interfaz obtuvo el desafío MFA, aceptó el código de Mailpit local y abrió el tablero. El formulario mostraba un error genérico para el 401, por lo que no identificaba la causa con claridad.

En el flujo exitoso, el cliente obtuvo CSRF, usó `credentials: include`, completó la verificación y conservó la sesión para las peticiones `/me` y `/dashboard`. No se observó respuesta 419 de CSRF ni 429 de throttling durante las pruebas; no se realizó una prueba de carga de límites. La contraseña y los códigos MFA no se guardaron en evidencias ni se imprimieron.

## Validación Android desde la interfaz

Las acciones se ejecutaron en el APK instalado, conectado a `http://10.0.2.2:8080` desde el emulador. La navegación con el botón Atrás físico regresó desde detalles y subpantallas.

| Flujo | Resultado visible |
| --- | --- |
| Login y MFA | Contraseña sintética aceptada, desafío MFA, código local aceptado y tablero “Hola, Prueba Admin” cargado desde el Gateway. |
| Personas | Listado con 8 personas sintéticas, búsqueda `Celia` con 1 resultado y apertura del detalle. |
| Agenda | Eventos reales del Gateway para la semana mostrada; se abrió un evento con estado, horario y lugar. |
| Invitaciones | Se creó un evento futuro ficticio para el usuario de prueba; apareció como pendiente, se abrió su detalle y se respondió **Aceptar**. La app mostró “Asistencia confirmada”. |
| Inventario | Listado de bienes, búsqueda por código con un resultado y ficha con existencia actual e historial. |
| Tesorería | Saldo inicial, ingresos, egresos y saldo actual; historial de tres asientos y detalle de comprobante. |

La invitación, las personas, los bienes y los movimientos monetarios son datos sintéticos del Compose aislado. Los importes de Tesorería son demostrativos y no representan dinero real.

## Implementación móvil

- `apps/mobile/src/api.ts`: tipos y métodos tipados para `GET /assets`, `GET /assets/{id}`, `GET /treasury` y `GET /treasury/movements/{id}`. Las rutas, filtros, UUID y fechas se validan antes de llamar al Gateway.
- `apps/mobile/src/Inventory.tsx`: búsqueda por código/nombre, tipo, estado, detalle y paginación/filtros del historial.
- `apps/mobile/src/Treasury.tsx`: saldos COP, filtros por texto y rango de fechas, historial paginado y detalle de asientos. No crea, modifica ni revierte movimientos.
- `apps/mobile/App.tsx`: navegación desde el tablero; Inventario y Tesorería solo se muestran a `superadmin`, `admin` y `treasurer`, como en la web. El Gateway conserva la autorización de cada endpoint.
- `apps/mobile/__tests__/api.test.ts`: pruebas de contratos para parámetros de Inventario y Tesorería, además de UUID y rango de fechas.

Inventario y Tesorería cubren primero consulta y lectura. Aún no incluyen las acciones de escritura, exportación, filtros avanzados completos ni todas las capacidades de escritorio/web.

## APK actualizado

| Propiedad | Resultado |
| --- | --- |
| Versión Android | `1.0` (`versionCode 1`), variante `debug` |
| Archivo | `SRD-Frontend/apps/mobile/android/app/build/outputs/apk/debug/app-debug.apk` |
| Tamaño | 126,610,028 bytes (120.7 MiB) |
| SHA-256 | `E180F000506E5B784382ED9E7BA7DA5BF7A5575D3BEC051DE3876327FD6EC91A` |
| Bundle JS | `assets/index.android.bundle` incluido en el APK |
| Firma | APK verificado con `apksigner`; certificado debug local |
| Instalación/apertura | `adb install -r` y apertura en `emulator-5554` correctas |
| Android | 17, API 37, emulador `sdk_gphone16k_x86_64` |
| Metro | No había listener en el puerto 8081 durante la apertura en frío |
| Teléfono físico | No disponible; `adb devices` mostró solo el emulador |

Comando de compilación: `:app:assembleDebug -PsrdEmbedDebugJs=true --no-daemon --max-workers=2`. Se reutilizó el proyecto corto `C:\srdm` para evitar el límite de longitud de rutas de Windows. `android/local.properties` apunta al SDK local y no forma parte del repositorio.

## Regresión y comprobaciones

| Comprobación | Resultado |
| --- | --- |
| `npx tsc --noEmit` en `apps/mobile` | Correcta |
| `npm test -- --runInBand` | 40 pruebas en 3 suites aprobadas |
| `npm run lint` | 0 errores; 3 advertencias preexistentes en `App.tsx` (`no-void` x2, `no-shadow` x1) |
| `git diff --check` en Frontend y Docs | Correcto |
| Gradle `:app:assembleDebug` | Correcta, bundle incluido |
| ADB | APK instalado, abierto y probado con Gateway local sin Metro |
| Teléfono físico | No probado; no se detectó uno conectado |

La cobertura de permisos se implementó siguiendo el rol de la web y los controles del Backend, pero no se ejecutó una matriz completa de roles ni de aislamiento entre dos juntas. La firma es de desarrollo; este APK no es un paquete firmado para distribución.

## Evidencia visual

Las capturas se tomaron desde el emulador Android. No contienen contraseñas ni códigos MFA.

- [Tablero tras instalar el APK nuevo](Evidencias/mobile-after-apk.png)
- [Tablero después del login/MFA desde Android](Evidencias/mobile-auth-dashboard.png)
- [Personas](Evidencias/mobile-people.png), [búsqueda](Evidencias/mobile-person-search.png), [detalle](Evidencias/mobile-person-detail.png)
- [Agenda](Evidencias/mobile-agenda.png) y [detalle de evento](Evidencias/mobile-event-detail.png)
- [Invitación sintética en Android](Evidencias/mobile-invitations-live.png), [detalle del evento](Evidencias/mobile-invitation-event.png) y [respuesta aceptada](Evidencias/mobile-invitation-accepted.png)
- [Inventario](Evidencias/mobile-inventory.png), [búsqueda](Evidencias/mobile-inventory-search.png) y [ficha/historial](Evidencias/mobile-inventory-detail.png)
- [Tesorería](Evidencias/mobile-treasury.png) y [detalle del comprobante](Evidencias/mobile-treasury-detail.png)

## Paridad pendiente

- Personas todavía no cubre todos los campos dinámicos, filtros, edición y administración de la web.
- Inventario y Tesorería son de solo lectura en móvil.
- Archivos/documentos, auditoría/informes y chat/tiempo real siguen sin pantallas móviles equivalentes.
- Falta probar permisos negativos y aislamiento multi-junta en una matriz de aceptación.
- No se pudo probar en un teléfono físico.

## Pull requests y worktrees

Los PR se mantienen abiertos; no se fusionó ninguno mientras la aceptación funcional o la revisión obligatoria estén pendientes.

| Repositorio | PR | Estado observado |
| --- | --- | --- |
| [SRD-Frontend #4](https://github.com/jsgh1/SRD-Frontend/pull/4) | Rama `feat/mobile-native-paridad-apk-20261027` | Abierto; `web-build` pasó después de la actualización. |
| [SRD-Database #4](https://github.com/jsgh1/SRD-Database/pull/4) | `fix/d8-shell-line-endings` | Abierto; `migration-syntax` pasó; añade `.gitattributes` con `*.sh text eol=lf`. |
| [SRD-Database #5](https://github.com/jsgh1/SRD-Database/pull/5) | Rama `feat/mobile-native-paridad-apk-20261027` | Abierto; `migration-syntax` pasó; su regla específica para `initialize-services.sh` queda cubierta por la regla global de PR #4. Existe solapamiento funcional; no se fusionó ni se cerró ninguno. |
| [SRD-Docs #4](https://github.com/jsgh1/SRD-Docs/pull/4) | Rama `feat/mobile-native-paridad-apk-20261027` | Abierto; sin checks configurados al momento de la consulta. |

Los repositorios modificados en esta continuación son Frontend y Docs. Los cinco worktrees están limpios después de publicar; Backend, Database y Docker-Infra no recibieron cambios locales en esta continuación.

## Guion de demostración

1. Abre SRD y accede con una cuenta sintética preparada para MFA.
2. Ingresa el código temporal y muestra el tablero autenticado.
3. Abre Personas, busca un registro y presenta el detalle.
4. Muestra Agenda, el evento y la invitación aceptada.
5. Abre Inventario, realiza una búsqueda y enseña la ficha/historial.
6. Muestra el saldo y un comprobante de Tesorería; aclara que el módulo móvil es de solo lectura y usa importes de demostración.

No uses las credenciales locales del fixture en una presentación ni conectes este APK de desarrollo a datos reales.
