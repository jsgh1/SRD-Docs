# Operación de SRD en el VPS de Contabo

## Reinicio de datos de producción del 22 de octubre de 2026

Por petición del propietario, se vaciaron las 11 bases de datos de SRD y los volúmenes de archivos privados y sesiones web. Se conservaron la configuración operativa, el certificado HTTPS, las claves del servidor y las copias cifradas. Se volvieron a ejecutar las migraciones y se crearon únicamente la junta `srd-piloto`, sus términos ES/EN y la cuenta inicial del superadministrador. El respaldo anterior a la limpieza es `srd-20261022T060312Z-L3Fg8e`, descargado a `.local/production/backups/` con los tres SHA-256 verificados. Las cuentas, fichas y datos de demostración anteriores solo permanecen en respaldos cifrados; no deben restaurarse si se desea continuar con el sistema vacío. La clave de acceso vigente sigue en `.local/production/initial-admin-credentials.txt`, fuera de los paquetes compartidos. Se comprobó una junta, un usuario, un superadministrador, cero personas, 27 servicios en marcha, HTTPS 200 y emisión de MFA con esas credenciales. El entorno local de desarrollo no se reinició.

Estado de esta guía: HTTPS, SMTP, MFA, navegación autenticada, operaciones principales con datos ficticios, cinco roles, dos juntas separadas, chat con adjunto y reconexión WSS, exportaciones, recuperación y llamada WebRTC entre redes están comprobados desde la URL pública. La evidencia y sus límites están en `DESPLIEGUE-CONTABO-2026-10-20.md`.

## Ubicación y arquitectura

- VPS Ubuntu 24.04.5: `144.91.126.25`; dominio: `srd-jsgh1.duckdns.org`.
- Código: `/opt/srd`. Compose: `/opt/srd/compose.production.yaml`. Variables reales: `/opt/srd/.env.production`, propiedad de root, modo 600.
- Caddy publica TCP 80 y 443. El relay Coturn usa la red del VPS y solo escucha en la IPv4 pública: UDP 3478 para STUN/TURN y UDP 49160–49200 para medios. El firewall permite además SSH en TCP 22. React y el gateway funcionan detrás de Caddy; MySQL, los diez dominios Laravel, Reverb y ClamAV solo usan redes Docker privadas.
- Los datos persistentes están en volúmenes Docker: `mysql_data`, `files_objects`, `gateway_sessions`, `antivirus_signatures`, los nueve `scheduler_health_*` y los volúmenes de certificados de Caddy. El prefijo real de Docker Compose es `srd-production_`.
- El entorno de desarrollo de Windows usa `compose.yaml` y está separado del VPS. El Compose de producción se genera con `node tools/generate-production-compose.mjs`.

## Acceso y secretos

Desde PowerShell, en el PC que conserva la clave privada:

```powershell
ssh -i "E:\Nueva carpeta\SRD\.local\deploy-ssh\srd_deploy_ed25519" srdadmin@144.91.126.25
```

La clave SSH, `.local/production/.env.production`, `.local/production/backup-identity.age` y `.local/production/initial-admin-credentials.txt` no van en los ZIP del código. Respalda la clave SSH y la clave de recuperación `backup-identity.age` en otro lugar seguro bajo tu control. Sin esta última, los archivos `.age` no se pueden descifrar. SSH acepta únicamente la clave de `srdadmin`; la contraseña de root ya no sirve para entrar por SSH. No pegues contraseñas en chats, comandos ni documentación.

Gmail ya está configurado. Para rotar su contraseña de aplicación, entra por SSH y ejecuta `sudo python3 /opt/srd/scripts/configure-production-smtp.py`; el script pide el correo y la nueva contraseña mediante entrada oculta, sin ponerla en argumentos. Después recrea `identity` y `identity-scheduler` con `sudo docker compose --env-file .env.production -f compose.production.yaml up -d --wait --no-build --force-recreate identity identity-scheduler` y prueba el envío. Revoca la credencial anterior en Google cuando la nueva funcione.

## Comandos habituales

Inicia sesión en el VPS, entra en `/opt/srd` y usa siempre el Compose de producción:

```bash
cd /opt/srd
sudo docker compose --env-file .env.production -f compose.production.yaml ps
sudo docker compose --env-file .env.production -f compose.production.yaml logs --tail=100 identity
sudo docker compose --env-file .env.production -f compose.production.yaml up -d --wait --no-build identity identity-scheduler
```

Para ver el estado de todos los servicios usa `ps`; para investigar, cambia `identity` por `mysql`, `gateway`, `web`, `caddy`, `antivirus` o el nombre del servicio afectado. No imprimas `.env.production` ni volcados SQL en terminales compartidas. Los logs de Docker tienen rotación configurada por servicio.

Para un primer despliegue desde una base vacía, el orden aplicado y comprobado fue: instalar Docker, iniciar MySQL, aprovisionar 11 bases y usuarios, iniciar los 11 servicios PHP, ejecutar `scripts/migrate-production.sh`, iniciar Reverb/web/nueve planificadores y después ClamAV. `scripts/create-demo-organization.sh` creó la junta ficticia `srd-piloto` con términos ES/EN que advierten no ingresar datos reales. El alta del primer superadministrador se realiza de forma interactiva con:

```bash
cd /opt/srd
sudo docker compose --env-file .env.production -f compose.production.yaml exec identity php artisan srd:bootstrap srd-piloto
```

**La cuenta inicial ya fue creada.** El comando anterior se conserva como referencia para una instalación nueva y rechazará ejecutarse de nuevo en este VPS. El correo y la contraseña de acceso están en `E:\Nueva carpeta\SRD\.local\production\initial-admin-credentials.txt`, protegido por ACL de Windows. La contraseña de aplicación Gmail no es la contraseña de inicio de sesión SRD. Usa la junta `srd-piloto` y abre `https://srd-jsgh1.duckdns.org/j/srd-piloto/login`; la primera sesión exige un código enviado a Gmail. No utilices credenciales de las pruebas locales.

Para configurar el relay de llamadas en una instalación nueva, ejecuta como root `python3 /opt/srd/scripts/configure-production-turn.py 144.91.126.25` desde el VPS. Genera la clave compartida en `.env.production` y el archivo privado `.turnserver.conf`; es idempotente y se niega a reemplazar un valor distinto. Después inicia `turn` con Compose y recrea `gateway` para cargar las variables. En este VPS ambas tareas ya se hicieron. El relay emite credenciales por usuario con una hora de vigencia; no compartas la clave `WEBRTC_TURN_SECRET` ni publiques `.turnserver.conf`. Tras restaurar una copia de `.env.production`, vuelve a ejecutar el configurador antes de iniciar TURN.

## Salud, red y actualización

Desde el PC se comprobó que `http://srd-jsgh1.duckdns.org` redirige a HTTPS, el certificado es válido, la web responde 200, la junta de prueba se consulta y WSS se suscribe al canal privado. Las pruebas automáticas completaron MFA, sesiones, 14 secciones, escrituras ficticias, aislamiento de dos juntas, cinco roles, chat y notificaciones, adjunto con descarga, reconexión WSS, exportaciones, recuperación de contraseña con revocación de sesión y una llamada con audio entre este PC y el VPS. Es una cobertura concreta, no una aceptación manual de cada interacción. Un contenedor `Up` por sí solo no prueba esas funciones.

Si Chat abre un socket WSS pero no recibe mensajes en tiempo real, comprueba la suscripción privada, no solo el handshake. El error `pusher:error` 4009 fue causado aquí por un origen no permitido. `chat-reverb` debe cargar `REVERB_ALLOWED_ORIGIN` con el host público exacto; la plantilla de Compose lo toma de `SRD_DOMAIN`. Tras cambiar ese valor, recrea únicamente `chat-reverb` y repite una prueba de mensaje entre dos sesiones.

Antes de actualizar el código, haz un respaldo cifrado y copia el ZIP de fuente actual. Revisa las migraciones nuevas y ejecuta `docker compose ... config -q` antes de compilar. Construye las imágenes nuevas y comprueba los healthchecks antes de servir tráfico. La reversión de código no revierte automáticamente el esquema SQL: si una migración incompatible ya se aplicó, restaura la copia de MySQL y archivos en una ventana de mantenimiento, tras probarla primero de forma aislada. Conserva los ZIP anteriores y nunca borres los volúmenes durante una actualización.

## Respaldo y recuperación

`sudo bash /opt/srd/scripts/backup-production.sh` crea en `/var/backups/srd/` tres archivos cifrados con `age`: SQL de las 11 bases de SRD, archivos privados y variables de entorno. También genera `SHA256SUMS`. La clave privada de descifrado solo existe en el PC, en `.local/production/backup-identity.age`; el VPS usa la clave pública de `/opt/srd/.backup-recipient`. El respaldo SQL usa una transacción coherente; el archivo de objetos se captura por separado, así que para un corte consistente de ambos conviene detener las escrituras durante una ventana de mantenimiento.

La copia `srd-20261021T181121Z-Y3L4to` se descargó a `.local/production/backups/` y pasó las tres verificaciones SHA-256. Su SQL se restauró en `srd-restore-test`, aislado de la red y sin puertos publicados: coincidieron 11 bases, 77 tablas y los registros ficticios esperados. El DOCX privado se extrajo del respaldo y coincidió por SHA-256 con el archivo de prueba; el contenedor de restauración está detenido. El temporizador diario ya se ejecutó con resultado satisfactorio el 22 de octubre y produjo `srd-20261022T040117Z-aGfgN3`. Antes del ajuste web se creó además `srd-20261022T050947Z-2aeXlo`: se descargó al PC y pasaron las tres verificaciones SHA-256, pero **no se ha restaurado**. `srd-backup.timer` sigue habilitado; **descarga manualmente** cada copia nueva fuera del VPS.

Los archivos descifrados usados en esa prueba quedaron en `.local/production/restore-test-20261021`, porque el control automático rechazó su eliminación. Son temporales sensibles; mantenlos únicamente en este PC y fuera de respaldos compartidos. Cuando puedas borrarlos manualmente, usa PowerShell desde la carpeta SRD: `Remove-Item -LiteralPath '.local/production/restore-test-20261021' -Recurse -Force`. Las copias `.age` verificadas se conservan.

Para retirar el VPS antes de cancelar el mes contratado: genera un respaldo nuevo, descárgalo al PC, verifica los SHA-256 y conserva la clave privada en almacenamiento separado. Comprueba que puedes descifrarlo. Después detén el temporizador de copias y los contenedores, sin borrar volúmenes hasta confirmar que ya no necesitas el servidor. La cancelación del VPS se hace en Contabo; este proyecto no la automatiza.

## Limitaciones actuales

- Las pruebas automatizadas comprobaron SMTP, HTTPS, MFA, 14 secciones, escrituras ficticias, cinco roles, dos juntas, adjunto de chat y reconexión WSS, recuperación completa y una llamada con audio entre redes. Aún conviene una aceptación manual amplia con usuarios y dispositivos del proyecto; no se afirma cobertura del 100 % de todas las acciones.
- Coturn está configurado y la llamada entre el navegador de este PC y el navegador del VPS recibió paquetes de audio en ambos sentidos. Conviene repetirla periódicamente desde redes y dispositivos finales distintos.
- El respaldo diario está habilitado y su primera ejecución automática tuvo resultado satisfactorio. Las copias posteriores deben descargarse y verificarse fuera del VPS; una copia solo en el servidor no protege frente a su pérdida.
- El 22 de octubre se desplegaron los ajustes de acceso, correo, invitaciones, planilla y exportaciones descritos en `CAMBIOS-WEB-2026-10-22.md`. Se reconstruyeron web, gateway, identidad y registros con sus planificadores. Los 27 servicios quedaron en ejecución sin estados de salud `unhealthy`; HTTPS, la junta pública y la prueba autenticada de estado de invitación y planilla vacía respondieron correctamente. La advertencia «Peligroso» en Chrome no se ha declarado resuelta: hay que consultar el informe de Problemas de seguridad de Google Search Console y solicitar revisión después de corregir la causa indicada.
