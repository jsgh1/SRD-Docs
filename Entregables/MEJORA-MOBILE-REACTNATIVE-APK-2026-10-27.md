# SRD Mobile React Native: APK y paridad web

**Fecha:** 27 de octubre de 2026

**Rama:** `feat/mobile-native-paridad-apk-20261027`

**Alcance:** `SRD-Frontend/apps/mobile`, validación con el Gateway y entorno local aislado.

## Resumen de entrega

Se estabilizó el cliente React Native nativo existente y se generó un APK debug autónomo, con el bundle JavaScript incluido. Se instaló y abrió en un emulador Android 17 (API 37). El cliente cargó una junta de prueba desde el Gateway local; además, una prueba API de extremo a extremo completó autenticación, MFA, Personas, Agenda, notificaciones y cierre de sesión.

Se corrigió la navegación del botón físico **Atrás** para que respete el contexto de la app. La compilación de TypeScript, Jest, ESLint y Gradle terminó. El inicio de sesión desde la UI móvil no quedó validado: en el intento manual mostró el error genérico «Debes iniciar sesión», mientras que el flujo API con el mismo fixture sí pasó. Este punto queda como prioridad de comprobación antes de la exposición.

## APK y entorno de compilación

| Propiedad | Resultado |
| --- | --- |
| Variante | `debug`, APK universal con ABIs arm64-v8a, armeabi-v7a, x86 y x86_64 |
| Archivo | `SRD-Frontend/apps/mobile/android/app/build/outputs/apk/debug/app-debug.apk` |
| Tamaño | 126,580,316 bytes (120.7 MiB) |
| SHA-256 | `B184B16C28CAC11E5F2C916D50C54518FE5EE5B6FA59660B0A3D5EE4BC278FE7` |
| Firma | Certificado debug local, verificada con `apksigner` |
| Bundle | `assets/index.android.bundle` incluido; no depende de Metro |
| SDK | compile 37, target 36, min 24 |
| Instalación | Correcta con `adb install -r` en AVD `Pixel_6`, Android 17 / API 37 |
| Apertura | Correcta con `monkey -p com.srd.mobile 1`; pantalla de acceso cargada |
| Dispositivo físico | No disponible; no se declara prueba en teléfono real |

El APK se construyó con `:app:assembleDebug -PsrdEmbedDebugJs=true --no-daemon --max-workers=2`. La ruta original de trabajo excedía el límite de longitud de Windows al procesar CMake/Ninja; se compiló una copia de trabajo corta en `C:\srdm` y se copió el artefacto resultante al repositorio. No se agregó la llave privada al repositorio. El APK es un artefacto de desarrollo firmado con clave debug local, no un paquete de distribución.

## Cambios de código

- `SRD-Frontend/apps/mobile/App.tsx`: manejo de Atrás físico para cerrar términos, abandonar MFA/recuperación/cambio de junta y volver desde vistas de detalle, agenda, notificaciones y preferencias.
- `SRD-Frontend/apps/mobile/__tests__/App.test.tsx`: caso Jest para volver desde Personas al tablero con Atrás.
- `SRD-Frontend/apps/mobile/.gitignore`: exclusión de `android/app/debug.keystore` local.
- `SRD-Database/.gitattributes`: normaliza a LF el script de inicialización MySQL para que Bash en el contenedor no reciba CRLF (`/bin/bash^M`).

## Validaciones ejecutadas

| Validación | Resultado |
| --- | --- |
| `npx tsc --noEmit` en `apps/mobile` | Correcta |
| `npm test -- --runInBand` | 38 pruebas, 3 suites aprobadas |
| `npm run lint` | 0 errores; 3 advertencias existentes (`no-void` x2 y `no-shadow` x1 en `App.tsx`) |
| Gradle `:app:assembleDebug` | Correcta; APK verificable y firmado |
| Instalación y apertura AVD | Correctas; bundle integrado, Metro no requerido |
| Carga de organización desde la app | Correcta; el emulador resolvió «Junta de prueba A» mediante `10.0.2.2:8080` |
| `mobile-expo-api.spec.mjs` contra el Gateway local | 1 prueba aprobada: login, MFA, dashboard, personas, agenda, notificaciones y logout |
| Login manual desde la UI Android | No aprobado: respuesta visible «Debes iniciar sesión»; revisar credenciales/configuración y repetir antes de la exposición |

La prueba API se ejecutó contra un Compose separado, con proyecto y volúmenes propios. Se levantaron servicios y datos sintéticos para esta tarea; no se usaron QA ni producción. Las credenciales de fixture y el `.env` permanecen locales y no se incluyen en el informe ni en los artefactos.

## Matriz de paridad web–móvil

Estados relativos a la web actual inspeccionada y a la implementación móvil presente en esta rama.

| Módulo | Estado móvil | Alcance y diferencia principal |
| --- | --- | --- |
| Acceso, MFA, recuperación y sesión | Parcial | Cliente implementa login, MFA, recuperación, sesión y logout. MFA/API verificados; el login manual de UI requiere repetición. |
| Tablero | Parcial | Resumen y accesos principales cargan; cobertura de tarjetas y acciones menor que web. |
| Junta y multi-tenant | Parcial | Selección por servidor/código y cambio de junta; consulta real de organización confirmada. Profundizar las pruebas de permisos por rol y aislamiento. |
| Personas | Parcial | Búsqueda, listado, detalle y registro pendiente básico. Sin equivalencia completa de campos dinámicos, filtros avanzados y edición. |
| Agenda, eventos e invitaciones | Parcial | Agenda, eventos e invitaciones disponibles; API de agenda validada. La gestión no iguala todas las interacciones web. |
| Notificaciones y preferencias | Parcial | Bandeja y preferencias remotas implementadas; validación API completada. |
| Inventario | Ausente | La web/backend y el fixture contienen datos, pero no hay módulo móvil. |
| Tesorería | Ausente | Sin pantallas ni flujos móviles para movimientos o saldos. |
| Archivos y documentos | Ausente | Sin explorador/carga/descarga móvil equivalente. |
| Perfil y configuración | Parcial | Perfil y preferencias básicas disponibles; faltan opciones y acciones de administración de cuenta de la web. |
| Auditoría e informes | Ausente | Requiere pantallas, filtros y exportación adaptados al móvil. |
| Chat y tiempo real | Ausente | No se implementó la experiencia móvil de conversación ni sus estados en tiempo real. |

La cobertura de roles, validaciones por campo, permisos granulares y aislamiento entre dos juntas requiere ampliar las pruebas de aceptación. La prueba actual valida un usuario de fixture y endpoints de lectura/flujo; no reemplaza una matriz completa por rol.

## Evidencias

- [Captura del APK abierto con la junta obtenida del Gateway](Evidencias/mobile-debug-final.png). La captura muestra el formulario de acceso y el entorno conectado; no representa una sesión autenticada.
- APK reproducible desde el artefacto local indicado arriba. Su hash permite confirmar que el archivo inspeccionado es el mismo.
- Resultado automatizado Playwright: `tools/browser-tests/mobile-expo-api.spec.mjs`, ejecutado contra `http://127.0.0.1:8080` en el Compose aislado.

## Instalación en un emulador o teléfono Android

1. Copia `app-debug.apk` al equipo con ADB instalado.
2. Habilita la instalación local del APK debug en el dispositivo.
3. Instala con `adb install -r app-debug.apk`.
4. Abre SRD desde el lanzador. No arranques Metro: el bundle ya viene incorporado.
5. Para conectarlo a un backend local desde el emulador, usa el Gateway accesible desde el emulador y el código de una junta de pruebas. El valor `10.0.2.2` aplica al emulador Android; en un teléfono físico se debe usar la IP/DNS accesible del servidor.

El APK fue firmado con una llave debug local. La instalación normal en un teléfono puede exigir aceptar la fuente del APK. No instalar en dispositivos con datos reales; este build apunta a desarrollo y el backend de demostración debe ser un entorno de pruebas.

## Guion de demostración de 90 segundos

1. **0–15 s:** abre SRD y explica que es una app Android React Native nativa con JavaScript integrado.
2. **15–30 s:** muestra el código de junta y cómo la app obtiene su nombre del Gateway.
3. **30–50 s:** completa acceso y MFA con un usuario de demostración configurado para la exposición. Este paso debe repetirse en la UI antes de presentar; no uses credenciales de este fixture local.
4. **50–65 s:** abre Personas y muestra búsqueda, listado y detalle.
5. **65–78 s:** abre Agenda y muestra los eventos e invitaciones de la junta.
6. **78–90 s:** abre notificaciones/preferencias y usa Atrás físico para volver al tablero. Menciona con claridad que Inventario, Tesorería, archivos, auditoría e informes y chat siguen pendientes en móvil.

## Pendientes priorizados

1. Repetir y cerrar el login/MFA desde la UI Android con el usuario de demostración; guardar evidencia de tablero autenticado.
2. Ejecutar aceptación en teléfono físico y confirmar configuración de red, teclado, fuentes y firma apropiada para la presentación.
3. Ampliar pruebas de autorización, aislamiento entre juntas, expiración de sesión y flujos negativos.
4. Implementar Inventario y Tesorería en React Native reutilizando contratos y reglas del backend.
5. Completar personas (campos dinámicos, filtros, edición), archivos/documentos, auditoría/reportes y chat en entregas posteriores.
6. Actualizar la app con una estrategia de release, firma protegida y APK de producción cuando exista el proceso de distribución autorizado.

## Publicación

La rama prevista es `feat/mobile-native-paridad-apk-20261027` en cada repositorio modificado. Añadir aquí los SHA de commit y enlaces a los PR después de publicarlos. No se debe fusionar a `dev` hasta que las comprobaciones obligatorias y la aprobación/CI del repositorio estén satisfechas.
