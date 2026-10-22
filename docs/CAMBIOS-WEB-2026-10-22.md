# Ajustes web solicitados el 22 de octubre de 2026

## Ajuste posterior: llamadas y perfil de contacto

- Al colgar la otra persona, una señal WebRTC pendiente puede recibir HTTP 409 porque la llamada ya terminó. La web ignora ese caso esperado y conserva los avisos para fallos reales.
- El estado del perfil del contacto queda debajo del nombre. El perfil muestra correo, cargo, rol de acceso y descripción. El correo se entrega solo en el detalle de un integrante activo de la misma junta, no en el listado.
- Cada integrante edita su descripción en Configuración > Mi perfil. Se guardan versiones ES/EN por junta; ambas son obligatorias si se usa este campo. La tabla nueva `member_profile_bios` se agregó sin modificar datos existentes.
- Compilación web local y en VPS correcta; suite de identidad: 52 pruebas y 622 aserciones aprobadas. La prueba autenticada pública verificó los campos nuevos del perfil, la invitación y la planilla vacía. Los 27 servicios quedaron activos y la migración aplicada.
- Respaldo cifrado previo: `srd-20261022T054945Z-nS4KvI`, descargado a `.local/production/backups/` con sus tres SHA-256 verificados. No se restauró.

Esta nota registra lo que se modificó en el código local y se desplegó en `https://srd-jsgh1.duckdns.org`. La operación del VPS y los respaldos están en `OPERACION-CONTABO.md`.

1. Acceso: la ilustración interactiva de estilo 3D se sustituyó por una ficha plana de registros. Se usa en código de junta, inicio de sesión, recuperación y nueva contraseña.
2. Correos: invitación, código MFA, recuperación, confirmación de nuevo correo y aviso de cambio de correo tienen asunto y contenido específicos y un diseño coherente. La invitación y el código recuerdan revisar spam si falta un correo esperado.
3. Planilla: el apartado de firmas ofrece PDF vacío de 15 filas para diligenciar a mano. El servidor entrega encabezados y logo sin incluir datos de personas; la vista web genera las filas numeradas antes de la descarga. La planilla con datos se conserva.
4. Roles: la ficha presenta «Función en la comunidad» como descripción de la persona. «Rol de acceso» sigue determinando permisos de la cuenta mediante invitación. Son conceptos distintos y el formulario lo explica.
5. Contraseñas: ambos campos de invitación y restablecimiento permiten mostrar u ocultar el texto. La advertencia «Peligroso» de Chrome exige revisar el informe de Seguridad de Google Search Console o Safe Browsing para conocer la causa y solicitar revisión; no se debe atribuir al icono de contraseña ni afirmar que desapareció por este cambio.
6. Personas: el detalle consulta el estado de invitación vinculado a la ficha y ofrece invitar solo cuando aún está disponible. Los listados y fichas exportados omiten UUID e identificadores internos y el número de versión. Las fotos del PDF individual se presentan como una galería compacta en lugar de una foto por página.
7. Chat: el estado se alinea bajo el nombre. La foto del perfil de usuario sigue siendo la fuente principal; si no existe, se usa la foto de la persona vinculada a la cuenta cuando el usuario tiene permiso de verla.
8. Versión visible: se quitaron las menciones visibles de la versión en términos y detalle de persona, así como el campo de versión de su exportación. El número interno de versión permanece en la base de datos para controlar cambios concurrentes.

Validación local: compilación de la web; 15 pruebas de exportación de personas/planilla (725 aserciones); 18 pruebas de membresías e invitaciones (259 aserciones); 37 pruebas de gateway (968 aserciones). No hubo navegador conectado para una revisión visual final, y el motor Docker Desktop local devolvió EOF; no se marca como validación completada. La advertencia de Chrome requiere diagnóstico externo de Google y comprobación posterior en Chrome.

Validación pública: los 27 servicios del VPS quedaron en ejecución sin `unhealthy`; la página principal, la invitación y la configuración de junta devolvieron HTTP 200 con certificado verificable. Una sesión autenticada de prueba comprobó que la API de estado de invitación responde y que la plantilla vacía devuelve encabezados sin datos de personas. El respaldo cifrado previo al despliegue se descargó y verificó con tres SHA-256.
