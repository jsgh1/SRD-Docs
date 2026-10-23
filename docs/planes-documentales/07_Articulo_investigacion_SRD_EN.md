# Update plan for the English research article

**Unmodified sources:** [Word](../entregables-originales/07_Articulo_investigacion_SRD_EN.docx) · [PDF](../entregables-originales/07_Articulo_investigacion_SRD_EN.pdf). The paper is a research article, with Abstract, Introduction, Related Work, Methodology, Design, Results, Discussion, Conclusions, and References. Keep that structure and integrate changes into the relevant sections.

## Requested editorial and technical work

1. State whether the unit of analysis remains the **9 October 2026 increment** or is extended through **19 October 2026**. If extended, update Abstract, Methods, Results, Discussion, Conclusions, and dates consistently. Do not preserve an old abstract with new results hidden later in the paper.
2. Describe the implemented gateway and ten domain services, databases divided by domain and records scoped to a board, React web client, and partial companion clients. Keep the specialized reporting service and production network architecture clearly labeled as planned when not deployed.
3. Report the later browser evidence precisely: **33 distinct test files passed among 140 present on one Docker-served web image**. The other 107 files lack a final recorded run on that image; this is not a whole-suite pass rate or project completion percentage. Cover the stale exact-person-result correction and retry after 503, the three outdated test expectations that were fixed and rerun, and Docker recovery only where verifiable reports support the claim.
4. Update limitations and threats to validity: synthetic local data, no formal end-user study, incomplete role-based write matrix, incomplete full regression and visual review, expired antivirus signatures blocking file uploads, and no claim of production-ready TURN/TLS or integrated load results. State explicitly which evidence is automated, operational, or planned.
5. Update methods and reproducibility from [VALIDACION.md](../VALIDACION.md), [REVISION_UX_WEB.md](../REVISION_UX_WEB.md), and [TRAZABILIDAD.md](../TRAZABILIDAD.md). Future work should specify measurable checks: full 140-file run on the final image, upload verification with valid signatures, accessibility, recovery, performance, and authorized user evaluation.
6. Recheck every external reference against its primary source before revising legal, standards, or related-work statements. Retain the tools disclosure, but do not use it to justify unsupported observations.

Coordinate terminology and numerical claims with the [Spanish article plan](07_Articulo_investigacion_SRD_ES.md). The final English prose should be natural academic English, not a literal paragraph-by-paragraph translation.
