# Despliegue de SRD en Contabo: estado y continuidad

Actualizado: 21 de octubre de 2026, hora de Colombia. Este archivo registra hechos comprobados, no sustituye los documentos académicos.

**Estado actual:** SRD está publicado en `https://srd-jsgh1.duckdns.org` con HTTPS válido. SMTP, MFA, sesión, navegación de las 14 secciones, escritura/lectura de datos ficticios, aislamiento entre dos juntas, cinco roles, chat con adjunto y reconexión WSS, notificaciones, exportaciones, recuperación de acceso y llamada WebRTC entre dos redes pasaron pruebas desde la URL pública. Esto no sustituye una aceptación manual exhaustiva de cada acción de la web.

## Copia previa a los cambios

`dist/SRD-checkpoint-2026-10-20.zip` conserva 1100 archivos fuente del estado inicial de esta intervención. SHA-256: `97881075ec98f648c2897c63cb31c9f50b16c3a8ff1a4b141240c9a17524ca0f`. El paquete excluye `.env`, `.local`, volúmenes Docker y datos de usuario. Es una copia del código, no una copia actual de MySQL. El archivo `dist/SRD-checkpoint-2026-10-20.zip.sha256` permite comprobar su integridad.

La carpeta abierta no contiene `.git`; por eso no se creó una rama. La copia inicial y los nuevos ZIP de fuente cumplen aquí la función de punto de retorno del código, mientras los respaldos cifrados cubren datos y secretos.

El corte de código actualizado está en `dist/SRD-produccion-2026-10-21.zip`, con SHA-256 en el archivo vecino `.sha256`. Conserva el paquete anterior como punto de retorno. El nuevo ZIP no contiene `.local`, credenciales reales, volúmenes, dependencias instaladas ni datos personales.

Tras las pruebas se generó `dist/SRD-produccion-validada-2026-10-21.zip` con su `.sha256`; se regeneró después de corregir Reverb. El empaquetador excluye `.local`, `.env.production` y `.turnserver.conf`. Los ZIP anteriores se conservan como cortes históricos.

## Estado verificado del VPS

- Host: `144.91.126.25`, Ubuntu 24.04.5 LTS, 23 GiB de RAM, 145 GB de disco (142 GB libres al inspeccionarlo).
- Dominio `srd-jsgh1.duckdns.org` resuelve a la IP indicada.
- Se creó `srdadmin` con clave SSH y privilegios `sudo`; una segunda conexión con clave y `sudo -n` funcionó. Después se desactivó el acceso SSH de root y la autenticación por contraseña; una conexión nueva de `srdadmin` volvió a funcionar.
- UFW está activo con reglas de entrada para TCP 22, 80 y 443, también en IPv6. Para WebRTC se añadieron únicamente en IPv4 UDP 3478 y UDP 49160–49200 hacia la dirección pública del VPS.
- Docker Engine 29.9.2 y Compose v5.6.0 se instalaron desde el repositorio oficial de Docker. `docker.service` está activo.
- Se transfirió una copia del código a `/opt/srd` y el SHA-256 del ZIP transferido coincidió con el local. Las claves de entorno exclusivas de producción están en `/opt/srd/.env.production`, con propietario root y modo 600. Gmail SMTP está configurado sin exponer su contraseña en el código ni en comandos.
- Compilaron las 12 imágenes propias: gateway, diez dominios y web. `gateway` y `files` respondieron HTTP 200 en `/up` dentro del VPS. La web respondió HTML y una ruta protegida del gateway devolvió HTTP 401 sin sesión.
- Los 11 servicios PHP, Reverb, la web y los nueve planificadores pasaron sus healthchecks internos. MySQL tiene 11 bases y 11 usuarios separados. Se aplicaron las migraciones iniciales; hay 77 tablas. ClamAV está saludable y el actualizador descargó firmas nuevas.
- Caddy obtuvo un certificado público válido. Desde el PC, HTTP respondió 308 hacia HTTPS y HTTPS respondió 200 con CSP, HSTS y las demás cabeceras. La junta `srd-piloto` respondió HTTP 200 a través del Gateway.
- El envío de un correo real a la misma cuenta SMTP terminó con `SMTP_SEND_OK`. Con la cuenta inicial se obtuvo `PRODUCTION_LOGIN_MFA_ISSUED` por HTTPS. La apertura de WebSocket WSS devolvió `PRODUCTION_WSS_OPEN`.
- Una segunda prueba leyó solamente el correo MFA recién emitido en la cuenta dedicada: `auth/verify` devolvió el rol `superadmin` de la junta correcta, `me` mantuvo la sesión, los servicios y miembros respondieron, y `auth/logout` hizo que `me` volviera a responder 401 (`PRODUCTION_MFA_SESSION_LOGOUT_OK`).
- La solicitud pública de recuperación entregó un correo nuevo con enlace HTTPS al dominio de SRD (`PRODUCTION_RECOVERY_DELIVERY_OK`). No se consumió el enlace ni se cambió la contraseña inicial.
- El intento de repetir inmediatamente el login para ampliar lecturas de módulos respondió HTTP 429 por el límite de emisión de desafíos; se detuvo la prueba para no consumir más intentos. Los módulos no se declararon validados por ese intento.
- Chrome automatizado cargó la pantalla de acceso pública a 1440 y 390 px sin errores de JavaScript ni desbordamiento horizontal.
- Desde el PC, los puertos 22, 80 y 443 estaban abiertos; 3306, 8000, 8080, 3310 y 1025 estaban cerrados o filtrados. Caddy publica 80/443; Coturn usa la red del host solo para los puertos UDP limitados de STUN/TURN. MySQL y los servicios de aplicación no publican puertos.
- Se creó un respaldo cifrado posterior al alta de la cuenta y SMTP en `/var/backups/srd/srd-20261021T041939Z-NKtjKz`. Su copia fuera del VPS está en `.local/production/backups/srd-20261021T041939Z-NKtjKz`; los tres SHA-256 coinciden. La clave privada de recuperación está solo en `.local/production/backup-identity.age` (ACL restringida al usuario local) y la clave pública está en `/opt/srd/.backup-recipient`. Guárdese otra copia segura de la clave privada: sin ella no es posible restaurar el respaldo cifrado.
- El temporizador `srd-backup.timer` quedó habilitado para copias diarias cifradas. Se ejecutó manualmente su unidad `srd-backup.service` con resultado `success`; la primera ejecución programada aún no ha ocurrido. Conviene vigilarla y descargar cada nueva copia fuera del VPS.
- Se restauró el SQL cifrado en un MySQL aislado, sin red ni puertos publicados; coincidieron las 11 bases y 77 tablas. El contenedor de recuperación quedó **detenido**. En una prueba posterior también se recuperó un DOCX privado ficticio y se comparó su SHA-256 byte por byte. No se usaron archivos de personas reales.
- En el primer arranque de MySQL, `initialize-services.sh` activaba `nounset` al ser cargado por el entrypoint y este falló después de crear las bases. El script se corrigió; MySQL quedó saludable, se ejecutó el aprovisionamiento de forma idempotente y se verificaron las 11 bases y cuentas. No se borró el volumen.

## Cambios locales de producción

- `compose.production.yaml` se genera desde `compose.yaml` mediante `tools/generate-production-compose.mjs`. El Compose local permanece separado. Se retiraron Mailpit y el TLS local, se configuró `APP_ENV=production`, HTTPS, cookies seguras y SMTP externo. Solo Caddy publica 80/443. MySQL, Laravel, Reverb y ClamAV quedan sin puertos publicados.
- `docker/php-production.Dockerfile` sustituye `php -S` por Apache con PHP, preservando HTTP interno en el puerto 8000 y el enrutamiento Laravel. Crea `bootstrap/cache` y `storage` antes de ejecutar Composer. `docker/web-production.Dockerfile` y `docker/nginx-production.conf` preparan la web para WSS y el dominio público. `docker/Caddyfile.production` configura el frente HTTPS. `services/chat/config/reverb.php` toma un único host permitido de `REVERB_ALLOWED_ORIGIN`; Compose fija esa variable al dominio público en `chat-reverb` y el entorno local conserva `localhost` por defecto.
- `services/gateway/bootstrap/app.php` acepta únicamente el encabezado de protocolo del proxy en producción, necesario para reconocer HTTPS detrás de Caddy/Nginx.
- `.env.production.example` no contiene secretos. `tools/initialize-production-env.mjs` genera valores aleatorios exclusivos en `.local/production/.env.production` y se niega a sobrescribirlos.
- `scripts/bootstrap-vps.sh` y `scripts/install-docker-ubuntu.sh` documentan las acciones ya ejecutadas en el servidor. `tools/tests/production-compose.test.mjs` comprueba que los servicios internos no publiquen puertos y que las variables de producción estén presentes.

## Pendientes operativos

1. Observar la primera ejecución **natural** de `srd-backup.timer` y descargar esa copia fuera del VPS. La unidad de respaldo ya pasó una ejecución real disparada por systemd y las copias generadas manualmente pasaron la verificación; aún no se ha observado el disparo diario programado.
2. Guardar copias seguras de la clave SSH, la clave privada `age` y las credenciales iniciales bajo control del propietario. Los ZIP de fuente no las contienen.
3. Hacer la aceptación manual de la web con usuarios del proyecto y dispositivos reales. Los recorridos automatizados detallados abajo comprueban casos concretos; no equivalen a cobertura del 100 % de todas las interacciones.

## Ampliación de validación del 21 de octubre de 2026

- Se repitió la lectura autenticada por HTTPS de `dashboard`, personas, accesos y cuota de archivos, inventario, tesorería, calendario con rango de fechas válido, notificaciones, auditoría, conversaciones, enlaces rápidos, planificadores y entregas de correo. El cierre de sesión devolvió 401 al consultar `me`.
- Chrome autenticado abrió las 14 secciones de la web a 1280 y 390 px: Inicio, Registro, Lista, Consultar, Contactos, Chat, Carpeta, Calendario, Tesorería, Inventario, Exportar, Configuración, Auditoría y Descargas. No hubo error de JavaScript, aviso de error de la vista ni desbordamiento horizontal en ese recorrido. Esto verifica la navegación y carga, no todas las acciones de cada pantalla.
- Antes de crear registros se tomó la copia cifrada `srd-20261021T180519Z-DXYQpX` y se descargó al PC con sus tres SHA-256 correctos.
- En `srd-piloto` se crearon y volvieron a leer un registro de persona ficticia desde el formulario web, un evento bilingüe, un bien inventariable bilingüe, un saldo inicial ficticio y una carpeta con un DOCX sintético. La descarga privada del DOCX coincidió por SHA-256 con el archivo de prueba y respondió la auditoría. Los identificadores quedaron en `.local/production/demo-fixture.json`, fuera del paquete de código. No se cargaron datos de una persona real.
- Se tomó la copia posterior `srd-20261021T181121Z-Y3L4to`; está descargada en `.local/production/backups/` y los tres SHA-256 coinciden. El SQL se descifró y restauró en el MySQL aislado `srd-restore-test`, sin red ni puertos: 11 bases, 77 tablas y los registros concretos de persona, calendario, inventario, documento y tesorería coincidieron. El contenedor volvió a quedar detenido. El archivo privado también se extrajo y el DOCX recuperado coincidió byte por byte por SHA-256.
- Las pruebas de restauración generaron archivos temporales en `.local/production/restore-test-20261021`. El control automático de esta sesión rechazó su eliminación; contienen SQL y archivos descifrados y deben permanecer **solo en este PC**, fuera de paquetes y sincronizaciones, hasta que el usuario los elimine manualmente. Los temporales del VPS sí se retiraron; la copia cifrada canónica permanece.
- Después de ese corte se completaron la separación entre juntas, los cinco roles, la recuperación completa en navegador y la llamada con audio entre redes distintas. Sigue pendiente observar el primer disparo natural del temporizador diario.

## Verificaciones adicionales del 21 de octubre

- Se creó una segunda junta **ficticia** (`srd-aislamiento-20261021`) para comprobar aislamiento. Desde su sesión, las listas de personas y tesorería aparecieron vacías, y los identificadores de persona, bien y documento de `srd-piloto` devolvieron 404. Al terminar el intervalo legítimo de MFA se creó un bien ficticio en la segunda junta y se comprobó el recorrido inverso: la primera junta recibió 404 al pedirlo. No se elevó ni desactivó el límite.
- Se recreó únicamente el servicio `files` para comprobar persistencia. El DOCX ficticio conservó su SHA-256 y se pudo descargar por HTTPS después del reinicio.
- Se ejecutó la unidad real `srd-backup.service` mediante un disparador temporal de systemd. Terminó con `Result=success` y produjo `srd-20261021T183907Z-87lYn3`; la copia cifrada se descargó fuera del VPS y sus tres SHA-256 coincidieron. El temporizador diario continúa habilitado y activo, con próxima activación anunciada para el 22 de octubre; todavía no se ha observado su disparo natural.
- Se preparó un navegador de prueba temporal dentro del VPS, accesible solo mediante un túnel SSH local, para probar una llamada WebRTC entre dos redes reales. Su funcionamiento HTTPS y un flujo de audio sintético se comprobaron.
- Se instaló Coturn oficial en Compose, fijado a una revisión y digest. La configuración privada y una clave TURN nueva quedaron solo en el VPS, con acceso restringido; el gateway emite credenciales de una hora. El servidor escucha en la IPv4 pública con puertos UDP limitados y reglas que impiden relé hacia redes privadas. Los navegadores de este PC y del VPS obtuvieron candidatos STUN públicos y, después, candidatos relay TURN. Una llamada real entre ambos navegadores conectó, recibió paquetes de audio en los dos sentidos y finalizó correctamente (`CROSS_NETWORK_WEBRTC_MEDIA_OK`).

## Cierre de pruebas funcionales de producción del 21 de octubre

- Cinco invitaciones llegaron al buzón de prueba y se aceptaron para los roles administrador, registrador, tesorero, auditor y lector. Cada cuenta inició con MFA, accedió a los recursos permitidos, recibió 403 en los recursos o escrituras prohibidos y no pudo cambiar a la junta sin membresía (`FIVE_PRODUCTION_ROLES_OK`). El primer intento de leer una invitación ocurrió antes de su entrega; se repitió tras verificar el correo, sin cambiar el producto.
- Con las sesiones de prueba se envió y volvió a leer un mensaje de chat, se comprobó su notificación, lectura y descarte, y la confirmación de lectura. Los XLSX de inventario y tesorería se descargaron (`CHAT_SEND_AND_PERSISTENCE_OK`, `CHAT_NOTIFICATION_READ_DISMISS_OK`, `CHAT_READ_RECEIPT_OK`, `INVENTORY_AND_TREASURY_EXPORTS_OK`).
- El flujo público de recuperación se completó en Chrome con una cuenta ficticia: solicitud, correo real, enlace, nueva contraseña, revocación de la sesión antigua y nuevo inicio con MFA (`RECOVERY_BROWSER_AND_SESSION_REVOCATION_OK`, `RECOVERY_NEW_PASSWORD_MFA_LOGIN_OK`). La contraseña inicial del superadministrador no se cambió.
- Se creó la copia cifrada posterior a estas pruebas `srd-20261021T234152Z-qTBPv7` en el VPS y se descargó a `.local/production/backups/` en este PC. Coincidieron los SHA-256 de `mysql.sql.age`, `files.tar.age` y `env.production.age`. La copia incluye el estado posterior a TURN y las cuentas de prueba. La copia anterior `srd-20261021T181121Z-Y3L4to` sí se restauró de forma aislada; la nueva copia `qTBPv7` aún no se ha restaurado.
- Al cerrar, los 27 servicios de Compose estaban en ejecución; 24 reportaron `healthy`, ninguno `unhealthy` y la URL pública de acceso respondió HTTP 200. Se detuvo solo el navegador temporal de QA `srd-qa-browser` y se cerró su túnel SSH; SRD y TURN permanecen activos.

## Verificación de adjuntos y reconexión del chat

- La prueba pública encontró un defecto de configuración: la conexión WSS abría, pero Reverb emitía `pusher:error` 4009 antes de suscribirse porque `allowed_origins` solo contenía `localhost`. Un simple handshake WSS no había detectado este fallo.
- Se configuró el dominio `srd-jsgh1.duckdns.org` como único origen permitido de `chat-reverb` en producción, se reconstruyó su imagen y se recreó únicamente ese contenedor. PHP lint y las pruebas de Compose pasaron; al terminar, `chat-reverb` estaba `healthy` y los 27 servicios seguían en ejecución.
- Con dos cuentas ficticias y MFA real, el navegador receptor se suscribió por WSS. El emisor envió un PNG ficticio, el receptor vio el mensaje y descargó el PNG saneado con sus dimensiones esperadas (`CHAT_PUBLIC_ATTACHMENT_UPLOAD_DELIVERY_DOWNLOAD_OK`). Después se cerró deliberadamente el socket del receptor, se observó una nueva conexión y suscripción WSS, y llegó un mensaje nuevo sin recargar la página (`CHAT_PUBLIC_WSS_RECONNECTION_AND_LIVE_DELIVERY_OK`). Este ensayo comprueba el restablecimiento de una conexión interrumpida; no representa todas las condiciones posibles de pérdida de red.
- Después del cambio y la prueba se tomó `srd-20261022T000013Z-YqMaVu`, se descargó a `.local/production/backups/` y coincidieron los tres SHA-256. Es la copia cifrada de datos más reciente; aún no se ha restaurado de forma aislada. La copia anterior `srd-20261021T181121Z-Y3L4to` sí pasó la restauración aislada.
- `srd-backup.timer` continuó `active` con `LastTriggerUSec` vacío y próxima activación `Thu 2026-10-22 06:01:08 CEST` (23:01:08 del 21 de octubre en Colombia). Por tanto, la ejecución diaria natural **sigue pendiente de observarse**; no se ha marcado como aprobada.

## Acceso de administración


En Windows, desde la raíz del proyecto:

```powershell
ssh -i .local\deploy-ssh\srd_deploy_ed25519 srdadmin@144.91.126.25
```

La clave privada de este acceso está en `.local`, que se excluye del ZIP y del repositorio. No compartirla. En el VPS, el código está en `/opt/srd`. Para inspeccionar servicios una vez iniciados:

```bash
cd /opt/srd
sudo docker compose --env-file .env.production -f compose.production.yaml ps
```

La URL pública está operativa para las comprobaciones indicadas. No se debe afirmar que todas las funciones de la aplicación están validadas en producción hasta ejecutar los recorridos pendientes.
