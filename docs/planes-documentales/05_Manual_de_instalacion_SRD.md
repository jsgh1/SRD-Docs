# Plan de actualización del Manual de instalación

**Originales:** [Word](../entregables-originales/05_Manual_de_instalacion_SRD.docx) · [PDF](../entregables-originales/05_Manual_de_instalacion_SRD.pdf). El original menciona expresamente `SRD-exposicion-2026-10-09.zip`; esa entrega es histórica y no debe presentarse como requisito de cualquier instalación nueva.

## Recorridos que debe escribir Chat

1. **Instalación limpia en otra PC Windows:** requisitos comprobados de Docker Desktop, PowerShell, Node para web/Expo cuando corresponda, almacenamiento, puertos 8080 y 8025 y acceso a dependencias públicas en el primer montaje. Partir del código actual de SRD, comprobar `compose.yaml`, `README.md`, `.env.example` y scripts. Ejecutar `Initialize.ps1` una sola vez en instalación nueva y `Up.ps1` según la guía vigente; registrar cómo esperar salud y aplicar migraciones. No meter credenciales locales en el manual.
2. **Arranque de instalación ya existente:** explicar por qué no se vuelven a generar secretos ni se vacían volúmenes. Revisar estado de Docker, respaldar antes de cambios, levantar servicios necesarios, verificar web, Mailpit, acceso y módulos. Separar «reiniciar contenedores» de «borrar datos».
3. **Demostración ficticia:** explicar `Start-Exposition.ps1 -WebOnly`, semilla y acceso local solo después de comprobar los efectos actuales del script. La contraseña se genera en `.local` de esa PC; no escribir una contraseña universal. Indicar Expo Go como paso opcional con teléfono, USB/ADB y Metro según [MOVIL.md](../MOVIL.md), sin hacer creer que Expo Go es obligatorio para la web.
4. **Comprobación operativa:** `docker compose ps`, HTTP 200 de la portada, correo MFA local, planificadores, lectura/escritura de prueba aislada, revisión de permisos y exportación. Incorporar una tabla «síntoma → diagnóstico → acción segura» para 503 de archivos por firmas antivirus, MFA que no llega, Docker sin respuesta, 429 temporal, servicio de dominio caído y escritura de resultado incierto.
5. **Respaldos y recuperación:** instrucciones reproducibles desde [RESPALDOS.md](../RESPALDOS.md), con verificación de archivo y simulacro aislado. No recomendar `docker compose down -v` ni cambios globales como arreglo de un error normal.

## Validación editorial

Probar literalmente los comandos en una copia o entorno aislado antes de publicarlos. El manual debe distinguir instalación local de publicación de servicios, TLS y TURN de producción. Los bloqueos de antivirus y la regresión web incompleta no desaparecen por levantar todos los contenedores. Fuentes: [ENTREGA-PORTABLE-2026-10-13.md](../ENTREGA-PORTABLE-2026-10-13.md), [PENDIENTES-WEB-2026-10-16.md](../PENDIENTES-WEB-2026-10-16.md) y scripts reales.
