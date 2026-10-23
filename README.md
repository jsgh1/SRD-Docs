# SRD · Documentación y entregables

> Repositorio de entregables académicos de **Sistema de Registro Digital (SRD)**, plataforma multi‑tenant para Juntas de Acción Comunal (JAC).

[Backend](https://github.com/jsgh1/SRD-Backend/tree/dev) · [Frontend](https://github.com/jsgh1/SRD-Frontend/tree/dev) · [Base de datos](https://github.com/jsgh1/SRD-Database/tree/dev) · [Docker Infra](https://github.com/jsgh1/SRD-Docker-Infra/tree/dev)

## 1. Objetivo

Mantener **un único catálogo limpio y fácil de navegar** de los entregables académicos actualizados de SRD. Se conserva el contenido original del conjunto documental del **19 de octubre de 2026**, sin regenerar, sobrescribir ni convertir los documentos.

Este repositorio **no alberga código de aplicación ni migraciones**: consulta los repositorios técnicos enlazados arriba para estudiar arquitectura, integración, Docker, APIs o ejecución.

## 2. Estructura definitiva

```text
SRD-Docs/
├── README.md
└── Entregables/
    ├── PDF/                     # 12 documentos en formato de lectura
    └── Word/                    # 12 versiones editables (.docx)
```

Se renombró la ruta larga `academic-2026-10-19/SRD_Documentacion_Actualizada_19-10-2026/` a **`Entregables/`**, conservando las dos categorías `PDF` y `Word`. La organización es por **formato**, no por repositorio técnico ni por versión de código.

## 3. Índice de entregables

Cada documento dispone de una versión PDF y su correspondiente archivo Word editable:

| Documento | PDF | Word |
| --- | --- | --- |
| 01\_Documentacion\_de\_pruebas\_SRD | [PDF](Entregables/PDF/01_Documentacion_de_pruebas_SRD.pdf) | [Word](Entregables/Word/01_Documentacion_de_pruebas_SRD.docx) |
| 01\_SRD\_Especificacion\_de\_Requisitos\_SRS | [PDF](Entregables/PDF/01_SRD_Especificacion_de_Requisitos_SRS.pdf) | [Word](Entregables/Word/01_SRD_Especificacion_de_Requisitos_SRS.docx) |
| 02\_Procedimiento\_de\_implementacion\_SRD | [PDF](Entregables/PDF/02_Procedimiento_de_implementacion_SRD.pdf) | [Word](Entregables/Word/02_Procedimiento_de_implementacion_SRD.docx) |
| 02\_SRD\_Analisis\_del\_Software | [PDF](Entregables/PDF/02_SRD_Analisis_del_Software.pdf) | [Word](Entregables/Word/02_SRD_Analisis_del_Software.docx) |
| 03\_Acciones\_correctivas\_preventivas\_mejoramiento\_SRD | [PDF](Entregables/PDF/03_Acciones_correctivas_preventivas_mejoramiento_SRD.pdf) | [Word](Entregables/Word/03_Acciones_correctivas_preventivas_mejoramiento_SRD.docx) |
| 03\_SRD\_Propuesta\_Tecnica | [PDF](Entregables/PDF/03_SRD_Propuesta_Tecnica.pdf) | [Word](Entregables/Word/03_SRD_Propuesta_Tecnica.docx) |
| 04\_Manual\_tecnico\_SRD | [PDF](Entregables/PDF/04_Manual_tecnico_SRD.pdf) | [Word](Entregables/Word/04_Manual_tecnico_SRD.docx) |
| 04\_SRD\_Diseno\_del\_Software | [PDF](Entregables/PDF/04_SRD_Diseno_del_Software.pdf) | [Word](Entregables/Word/04_SRD_Diseno_del_Software.docx) |
| 05\_Manual\_de\_instalacion\_SRD | [PDF](Entregables/PDF/05_Manual_de_instalacion_SRD.pdf) | [Word](Entregables/Word/05_Manual_de_instalacion_SRD.docx) |
| 06\_Manual\_de\_usuario\_final\_SRD | [PDF](Entregables/PDF/06_Manual_de_usuario_final_SRD.pdf) | [Word](Entregables/Word/06_Manual_de_usuario_final_SRD.docx) |
| 07\_Articulo\_investigacion\_SRD\_EN | [PDF](Entregables/PDF/07_Articulo_investigacion_SRD_EN.pdf) | [Word](Entregables/Word/07_Articulo_investigacion_SRD_EN.docx) |
| 07\_Articulo\_investigacion\_SRD\_ES | [PDF](Entregables/PDF/07_Articulo_investigacion_SRD_ES.pdf) | [Word](Entregables/Word/07_Articulo_investigacion_SRD_ES.docx) |

**Total:** **12 PDF + 12 Word = 24 archivos originales conservados**.

### ¿Qué documento consultar?

- **Especificación de Requisitos (SRS):** necesidades del sistema, requisitos y alcance.
- **Análisis, propuesta técnica y diseño del software:** descripción, solución y decisiones del proyecto.
- **Procedimiento de implementación y manual de instalación:** instrucciones y criterios técnicos de montaje.
- **Manual técnico:** apoyo para comprender componentes e integración.
- **Manual del usuario final:** guía para operar las funciones disponibles.
- **Documentación de pruebas y acciones correctivas:** evidencias y enfoque de calidad.
- **Artículo de investigación:** versiones en español e inglés.

> Los nombres y temas del índice corresponden a los archivos existentes. No se modificaron internamente los PDF ni los Word en esta reorganización.

## 4. Relación con los repositorios de código

```text
SRD-Frontend (React, Electron y apps móviles)
      │
      ▼
SRD-Backend (Gateway y 11 microservicios Laravel)
      │
      ▼
SRD-Database (migraciones Laravel y permisos MySQL)
      ▲
      │
SRD-Docker-Infra (ensamblaje, Compose, Nginx y pruebas)

SRD-Docs (entregables académicos, documentación para lectura)
```

| Repositorio | Qué consultar |
| --- | --- |
| [SRD-Backend](https://github.com/jsgh1/SRD-Backend/blob/dev/README.md) | Arquitectura backend, dominios, patrones, APIs y pruebas |
| [SRD-Frontend](https://github.com/jsgh1/SRD-Frontend/blob/dev/README.md) | Arquitectura de web, móvil, escritorio y construcción |
| [SRD-Database](https://github.com/jsgh1/SRD-Database/blob/dev/README.md) | Migraciones, MySQL y separación de permisos |
| [SRD-Docker-Infra](https://github.com/jsgh1/SRD-Docker-Infra/blob/dev/README.md) | Guía principal para ensamblar y arrancar los cinco repositorios |

## 5. ¿Cómo instalar y levantar SRD?

**No se ejecuta Docker desde SRD-Docs.** Para desarrollar o probar el sistema:

1. Lee el [README de SRD-Docker-Infra](https://github.com/jsgh1/SRD-Docker-Infra/blob/dev/README.md).
2. Clona los cinco repositorios, preferiblemente usando los commits exactos de una misma liberación.
3. Ensambla las carpetas de Backend, Frontend y las migraciones de Database en un `workspace` independiente junto a Infra.
4. Configura un proyecto Compose exclusivo en máquinas compartidas y usa `scripts/Up.ps1`.
5. Verifica salud de contenedores, autenticación, aislamiento entre juntas y funcionamiento real antes de promover una versión.

Los entregables PDF/Word son documentos de referencia y no reemplazan el código, las migraciones ni las pruebas automatizadas.

## 6. Limpieza documental y recuperación del histórico

Esta estructura se obtuvo mediante una limpieza **solo del árbol de trabajo del repositorio Docs**:

- Se dejaron exclusivamente `README.md` y `Entregables/PDF`, `Entregables/Word`.
- Los **24 entregables** se trasladaron conservando sus objetos Git originales, nombres y contenidos.
- Se retiraron del árbol activo las notas antiguas, duplicados, revisiones históricas, documentación de módulo y carpetas auxiliares que no son el catálogo oficial.
- **No se borró la historia de Git.** Antes de limpiar se creó la rama de respaldo [`backup/docs-before-cleanup-2026-10-23`](https://github.com/jsgh1/SRD-Docs/tree/backup/docs-before-cleanup-2026-10-23) con el estado anterior completo. Allí se pueden consultar/restaurar archivos eliminados del árbol actual.

**Nota:** la limpieza es una decisión de organización del repositorio, no implica que esos documentos históricos carecieran de valor técnico.

## 7. Convenciones

- **PDF:** versión preparada para revisión y entrega.
- **Word:** archivo `.docx` editable; conservar pares equivalentes.
- No incorporar contraseñas, datos de residentes, llaves, tokens ni respaldos reales de bases de datos a los entregables.
- Cambios documentales: crear rama hija de `dev`, mantener nombres coherentes, comprobar que Word/PDF abren y documentar modificaciones en PR.
- Para nuevas versiones de documentos, revisar su consistencia con los cinco repositorios y la evidencia real de pruebas; no declarar producción validada basándose solo en capturas antiguas.

---

**Proyecto:** [SRD](https://github.com/jsgh1) · **Entregables conservados:** 24 archivos · **Versiones previas recuperables:** rama de respaldo.
