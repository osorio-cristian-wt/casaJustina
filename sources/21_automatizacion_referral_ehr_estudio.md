# Evidencia: automatizar el referral de donantes desde el EHR (estudio Southwest Transplant Alliance)

- **URL:** https://pmc.ncbi.nlm.nih.gov/articles/PMC10109458/
- **URL secundaria:** https://journals.lww.com/transplantationdirect/fulltext/2022/08000/short_report__evaluating_the_effects_of_automated.7.aspx
- **URL secundaria:** https://www.sciencedirect.com/science/article/pii/S1600613522251028 (CDS para muerte encefálica inminente)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** académico (Transplantation Direct, 2022 — short report revisado por pares)

## Resumen del contenido

Estudio que mide el efecto de **automatizar la derivación de potenciales donantes** directamente desde el EHR hospitalario al sistema del OPO (la "OPO" estadounidense ≈ el CUCAI argentino). Es la **mejor evidencia cuantitativa disponible** de que detectar automáticamente por triggers clínicos (incluido Glasgow) y avisar al referente de donación **aumenta los donantes reales**, no solo las alertas.

## Información clave

### Diseño del estudio
- **OPO:** Southwest Transplant Alliance (Texas, USA).
- **Hospitales piloto:** 3 de 137 del área del OPO.
- **Período:** enero 2015 – marzo 2021. **Implementación:** 9 de octubre de 2018.
- **N:** 28.034 derivaciones de pacientes ventilados.
- **Método:** diferencias-en-diferencias con regresión de Poisson (piloto vs. resto del OPO).

### Trigger automatizado
Pop-up automático en el EHR cuando el paciente cumple: **(1) ventilación mecánica + (2) UCI por lesión neurocrítica + (3) Glasgow ≤5**. Implementado como **customización con API entre EHR y el sistema del OPO**, usando el estándar de codificación **mCODE**.

### Resultados (medias mensuales pre vs. post-octubre 2018)

| Métrica | Pre | Post | Aumento |
|---|---|---|---|
| **Referrals (derivaciones)** | 11,7/mes | 26,7/mes | **+45 %** |
| **Approaches (acercamientos a familia)** | 1,4/mes | 4,5/mes | **+83 %** |
| **Authorizations (autorizaciones)** | 1,0/mes | 2,3/mes | **+73 %** |
| **Organ donors (donantes efectivos)** | 0,7/mes | 1,4/mes | **+92 %** |

- Composición post-implementación de los 26,7 referrals/mes: **73,8 % automáticos**, 26,2 % manuales.
- El estudio relacionado (ScienceDirect, S1600613522251028) reporta que un **CDS para muerte encefálica inminente** mejoró el **tiempo a la notificación** y aumentó la donación.

### Limitaciones declaradas por los autores
1. Muestra chica (3 hospitales, una región) → generalización limitada.
2. Requirió **"considerable custom code development"** → **no replicable sin desarrolladores de EHR** dedicados (clave: justifica un adaptador genérico/reutilizable en vez de custom por hospital).
3. Resultado significativo sólo en período pre-COVID.
4. Posibles diferencias clínicas no exploradas entre derivaciones automáticas y manuales.
5. Personal del OPO manifestó preocupación por **sobrecarga de derivaciones** (→ ver alert fatigue, [25_alert_fatigue_cds_uti.md](25_alert_fatigue_cds_uti.md)).

## Relevancia para el sistema de trasplantes

Convierte la hipótesis del proyecto en un **caso de negocio con números**: si automatizar el trigger casi **duplicó los donantes efectivos (+92 %)** en los hospitales piloto, el mismo mecanismo aplicado a la subdetección argentina (cuello de botella #1) tiene un techo de impacto enorme. Es el dato más fuerte para un pitch ante AFIDEER, Casa Justina e INCUCAI.

## Implicancias tecnológicas

- **El valor está en la automatización del referral, no en inventar el criterio clínico.** El criterio (Glasgow + vent + neurocrítico) ya existe; lo que mueve la aguja es eliminar la dependencia de que un humano se acuerde de avisar.
- **Lección de la limitación #2:** el enfoque "customización por hospital con su equipo de EHR" no escala. El aporte diferencial del proyecto argentino debe ser un **adaptador estandarizado** (FHIR Subscription / HL7) reutilizable entre HIS, no código a medida por institución.
- **Lección de la limitación #5:** aún en un sistema exitoso, los referentes se quejan de sobrecarga → el diseño de la alerta importa tanto como el trigger.
- **mCODE** es un IG FHIR oncológico; para trasplante no hay IG equivalente en AR (gap ya señalado en [08_interoperabilidad_fhir_argentina.md](08_interoperabilidad_fhir_argentina.md)) → oportunidad de definir un perfil FHIR de detección de donante.

## Citas relevantes

> "The automated referral was associated with a 45% increase in referrals, an 83% increase in approaches for authorization, a 73% increase in authorizations, and a 92% increase in organ donors." (Transplantation Direct, 2022)

> "...the automation was accomplished using a function of the EHR where a new pop-up window appears..." (ídem)

> "...required considerable custom code development..." (limitación declarada)

## Observaciones

- **+92 % de donantes** es de 3 hospitales seleccionados; **no extrapolar linealmente** a todo el país sin piloto local. Marcar como "evidencia de dirección y magnitud", no como promesa.
- El delta absoluto (0,7 → 1,4 donantes/mes por grupo de hospitales) recuerda que los números son chicos por hospital pero **clínicamente enormes** (cada donante ≈ varios trasplantes).
- Antecedente directo del valor de la idea del equipo: ver [20_triggers_clinicos_premortem_nhs_usa.md](20_triggers_clinicos_premortem_nhs_usa.md).
