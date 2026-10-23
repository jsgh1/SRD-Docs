# Plan de actualización del Procedimiento de implementación

**Originales:** [Word](../entregables-originales/02_Procedimiento_de_implementacion_SRD.docx) · [PDF](../entregables-originales/02_Procedimiento_de_implementacion_SRD.pdf). El original ya distingue preparación, instalación, verificación, demo, actualización, respaldo y reversión; conservar esa estructura útil.

## Cambios concretos

1. Separar tres recorridos: **instalación limpia**, **actualización conservando datos** y **preparación de demostración ficticia**. Para cada uno, indicar precondiciones, entradas, comandos del repositorio, resultado verificable y qué no debe repetirse. No ejecutar `Initialize.ps1` sobre una instalación existente sin evaluar sus efectos; no regenerar secretos ni cargar semillas sobre juntas reales.
2. Revisar contra los scripts actuales de `scripts/` antes de transcribirlos: `Initialize.ps1`, `Up.ps1`, `Start-Exposition.ps1 -WebOnly`, `Start-ExpoGo.ps1`, `Backup-Local.ps1` y `Test-Browser.ps1`. Distinguir que `Start-Exposition.ps1` prepara una demo específica y no es el método genérico de producción. Evitar rutas de un ZIP de exposición antiguo como requisito universal.
3. Actualizar comprobación posterior: `docker compose ps`, portada local `http://localhost:8080`, Mailpit local `http://localhost:8025`, migraciones, planificadores requeridos, login/MFA, lectura y una escritura ficticia aislada, autorización por junta y auditoría. Una respuesta HTTP 200 no cierra todos esos controles.
4. Documentar la excepción antivirus: al 19/10/2026 las firmas locales vencidas provocan 503 en cargas. El procedimiento debe **detener esa verificación**, registrar bloqueo y exigir firmas vigentes antes de repetir, nunca desactivar el análisis para forzar éxito.
5. Incluir plan de recursos para lotes pequeños de navegador tras la pérdida de respuesta de Docker Desktop observada. Al reiniciar, comprobar qué servicios están realmente saludables; no borrar volúmenes como recuperación rutinaria.
6. Reforzar respaldo, reversión y registro de instalación: ubicación y verificación del respaldo cifrado, versión de código/imagen, migraciones, operador, hora, resultado, prueba de restauración aislada y supresiones pendientes. Una reversión del código no deshace automáticamente un cambio de base de datos.

**Fuentes:** [RESPALDOS.md](../RESPALDOS.md), [PENDIENTES-WEB-2026-10-16.md](../PENDIENTES-WEB-2026-10-16.md), [ARQUITECTURA.md](../ARQUITECTURA.md) y los scripts efectivos. Mantener comandos comprobables y separar ambiente local de un despliegue público no autorizado.
