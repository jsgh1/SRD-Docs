# QA previa al merge SRD — 2026-10-23

## Alcance y entorno

Migración SRD desde monolito/estructura anterior hacia cinco repositorios separados: SRD-Backend, SRD-Frontend, SRD-Database, SRD-Docker-Infra y SRD-Docs. Destino de integración: `dev`. No se modifica `main` ni el VPS de producción.

Entorno de integración local del SENA: proyecto Docker Compose `srd-migration-sena`, datos en volumen `srd-migration-sena_mysql_data`, frontend en `127.0.0.1:8080`, Mailpit en `127.0.0.1:8025`. Estas pruebas locales se reportan conforme a las salidas observadas en PowerShell y la interfaz.

## Comprobaciones aprobadas con evidencia

| Área | Resultado | Evidencia |
|---|---|---|
| Backend PHP 8.4 | **305 pruebas y 6292 aserciones aprobadas**, 11 suites PHPUnit | [Run #39979516811](https://github.com/jsgh1/SRD-Backend/actions/runs/39979516811) |
| Frontend React/TypeScript | `npm ci` y `npm run build` aprobados | [Run #39979167158](https://github.com/jsgh1/SRD-Frontend/actions/runs/39979167158) |
| Database | Sintaxis PHP de **72 archivos de migración** aprobada | [Run #39979177188](https://github.com/jsgh1/SRD-Database/actions/runs/39979177188) |
| Infra Docker | `docker compose config` y análisis estructural de Reverb aprobados | [Run #39980062621](https://github.com/jsgh1/SRD-Docker-Infra/actions/runs/39980062621) |
| Reverb reconstruido | Imagen Chat construida desde el snapshot Backend; Reverb inicia y abre su socket en contenedor limpio sin override local | [Run #39980062621](https://github.com/jsgh1/SRD-Docker-Infra/actions/runs/39980062621) |
| Docker local SENA | 11/11 microservicios y 9/9 schedulers saludables; React/Nginx/Mailpit HTTP 200 | Consola PowerShell de validación local |
| Bases locales | 72 migraciones aplicadas en 11 esquemas MySQL; junta demo `srd-piloto` creada | Consola PowerShell de migración |
| Autenticación | Primer inicio de sesión como superadministrador confirmado por captura del panel | Validación manual local |
| ClamAV | Contenedor healthy, `daily.cld` actualizado, **Eicar-Test-Signature FOUND** | Consola PowerShell local |

## Hallazgo corregido

Antes de la QA, `chat-reverb` exigía una imagen global `srd-chat`, no disponible en la reconstrucción del SENA. Se corrigió en SRD-Docker-Infra usando el mismo contexto/Dockerfile de compilación de Chat: [commit 72bab141](https://github.com/jsgh1/SRD-Docker-Infra/commit/72bab1415d20e02603b655a6b135e0f58e815a97). Además del smoke test local anterior, la compilación e inicialización limpia en CI se verificaron en el Run #39980062621.

## Comprobación local de aislamiento MySQL — APROBADA

**Fecha:** 2026-10-23. **Entorno:** estación de trabajo SENA, `C:\Users\Aprendiz\Desktop\SRD-Migration-SENA-2026-10-23\workspace`. **Proyecto Docker:** `srd-migration-sena`.

- Compose `config` verificó el nombre de proyecto y el volumen existente `srd-migration-sena_mysql_data`.
- Los **26 contenedores** listados estaban en ejecución. Servicios Laravel, planificadores, antivirus, MySQL y Mailpit con healthchecks reportaron `healthy`; el contenedor web y el antivirus-updater estaban en ejecución (no reportan healthcheck en esa salida).
- Los accesos `http://127.0.0.1:8080/` y `http://127.0.0.1:8025/` devolvieron **HTTP 200**.
- Se ejecutó en cada microservicio el comprobador de solo lectura `tools/check-mysql-isolation.php`, que verifica la base propia y prueba acceso a la tabla `migrations` de las otras diez bases.
- **11/11 microservicios PASS; 110/110 intentos de lectura cruzada entre esquemas denegados; 0 fallos.**
- Los microservicios comprobados fueron: gateway, identity, configuration, records, audit, files, calendar, notifications, treasury, inventory y chat.
- Esta prueba acredita **aislamiento de credenciales MySQL entre microservicios**, **NO** equivale al aislamiento de filas/datos entre dos juntas de acción comunal dentro de un mismo microservicio (tenant isolation). El aislamiento entre juntas sigue pendiente de una prueba integrada de API/navegador con dos organizaciones.

## Pendientes honestamente identificados

- **Flujos end-to-end en navegador:** pruebas Playwright de login, registros CRUD, roles, carpeta/archivos, calendario, chat WebSocket con suscripciones autenticadas, y aislamiento entre dos juntas. Las suites existentes crean fixtures; ejecutarlas únicamente en un entorno de pruebas separado o controlado; nunca sobre datos reales sin un plan aprobado.
- **Correspondencia contra snapshot original:** cotejo documental `SOURCE_MAPPING.csv` contra las cinco extracciones. El archivo referenciado en las descripciones iniciales de PR no estaba presente en esos cinco árboles Git durante esta revisión.
- **Aprobación de QA humana:** revisar resultados y decidir integración; las CI unitarias y smoke tests no equivalen a aprobación integral de producción.

## Criterio para la rama dev

Los resultados automatizados y el arranque en el SENA son evidencia muy favorable para **integrar a `dev`** tras cerrar los criterios definidos por el equipo. No autorizan por sí solos un despliegue a producción. Conservar respaldo de ramas y no tocar el VPS.

## Pull requests

- [SRD-Backend #2](https://github.com/jsgh1/SRD-Backend/pull/2)
- [SRD-Frontend #2](https://github.com/jsgh1/SRD-Frontend/pull/2)
- [SRD-Database #2](https://github.com/jsgh1/SRD-Database/pull/2)
- [SRD-Docker-Infra #2](https://github.com/jsgh1/SRD-Docker-Infra/pull/2)
- [SRD-Docs #2](https://github.com/jsgh1/SRD-Docs/pull/2)
