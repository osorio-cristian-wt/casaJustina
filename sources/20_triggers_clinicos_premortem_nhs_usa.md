# Disparadores clínicos de referral pre-mortem — NHS (UK) y modelo de triggers EHR (USA)

- **URL:** https://www.odt.nhs.uk/deceased-donation/best-practice-guidance/identify-and-refer-a-potential-organ-donor/
- **URL secundaria:** https://pubmed.ncbi.nlm.nih.gov/31773544/ (Devastating Brain Injury pathway, neuro-ICU, 3 años)
- **URL secundaria:** https://pmc.ncbi.nlm.nih.gov/articles/PMC13007231/ (Variability in clinical triggers for organ donation referrals)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** técnico/clínico (guía NHS BT + literatura)

## Resumen del contenido

Define **qué eventos clínicos disparan, ANTES de la muerte, la derivación de un potencial donante**. El modelo NHS (UK) establece "clinical triggers" objetivos que obligan a llamar al especialista en donación (SNOD); el modelo de EHR estadounidense traduce triggers equivalentes a criterios automatizables. La idea central —y la que pidió el equipo— es que **la identificación ocurre mientras el paciente todavía está vivo**, para que el referente de donación lo siga desde temprano.

## Información clave

### Triggers NHS BT (referir = llamar al SNOD)

El referral debe hacerse cuando se cumple **cualquiera** de estos, **antes de la muerte**:

| Trigger | Criterio exacto |
|---|---|
| **Daño cerebral devastador (DBI)** | "uno o más reflejos de nervios craneales **ausentes** Y un **Glasgow Coma Score de 4 o menos** que no pueda explicarse por sedación" |
| **Decisión de diagnóstico de muerte neurológica** | "se ha tomado la decisión de diagnosticar la muerte usando criterios neurológicos" |
| **Decisión de retirar soporte vital (WLST)** | "pacientes para los cuales se ha tomado la decisión de retirar el tratamiento de soporte vital" |

- **Momento del referral: pre-mortem.** La guía insiste en "timely identification and timely referral of potential donors" para permitir "stabilisation for neurological death testing".
- **Rol del SNOD (Specialist Nurse in Organ Donation):** tras el referral hace evaluación clínica, accede al registro de donantes y **planifica junto al intensivista y enfermería el acercamiento familiar**.
- **Desacople ("decoupling"):** la donación solo se explora con la familia **después** de que aceptó la mala noticia. El SNOD debe estar presente en todos los acercamientos familiares.
- **Línea de referral única:** 03000 203 040 (un solo canal nacional, 24/7).

### Triggers modelo EHR (USA, automatizables)

Combinación de tres condiciones objetivas extraíbles del registro:
1. Paciente en **ventilación mecánica**.
2. **Admisión a UCI por lesión neurocrítica**.
3. **Glasgow Coma Score ≤5**.

→ Estos tres son campos estructurados del HIS, lo que los hace automatizables sin juicio clínico adicional (ver estudio en [21_automatizacion_referral_ehr_estudio.md](21_automatizacion_referral_ehr_estudio.md)).

### Comparación de umbrales de Glasgow

| Sistema | Umbral de Glasgow para detección/referral | Condición adicional |
|---|---|---|
| **Argentina (INCUCAI PSG<7)** | **≤7** | paciente neurocrítico |
| **NHS UK (DBI)** | **≤4** | + ≥1 reflejo de tronco ausente, no por sedación |
| **EHR USA (trigger study)** | **≤5** | + ventilación mecánica + UCI neurocrítica |

> El umbral argentino (≤7) es **el más sensible** (captura antes, más casos) → más alertas potenciales → más exige un buen diseño anti-"alert fatigue" ([25_alert_fatigue_cds_uti.md](25_alert_fatigue_cds_uti.md)).

## Relevancia para el sistema de trasplantes

Es el "manual de operación" de la idea del proyecto: **detectar al donante antes de su fallecimiento y avisar al referente** (SNOD en UK, coordinador hospitalario en Argentina). Demuestra que la detección pre-mortem no es invasiva ni ilegal cuando se enmarca como parte del cuidado de fin de vida: el referral habilita una **evaluación**, no una acción sobre el paciente, y la donación recién se conversa tras la aceptación de la muerte por la familia.

## Implicancias tecnológicas

- **Los 3 triggers EHR son directamente mapeables a recursos FHIR:** ventilación (`Observation`/`DeviceUseStatement`), UCI neurocrítica (`Encounter.location` + `Condition`), Glasgow (`Observation` LOINC 9269-2). Ver [22_fhir_subscription_codificacion_glasgow.md](22_fhir_subscription_codificacion_glasgow.md).
- **El umbral debe ser configurable** (≤7 nacional vs ≤5 conservador) para calibrar sensibilidad/ruido por hospital.
- **El diseño debe respetar el desacople:** la herramienta detecta y avisa al coordinador; **no** dispara conversación con la familia ni decisiones clínicas. Esto es clave para la aceptación médica y legal.

## Citas relevantes

> "...one or more cranial nerve reflexes are absent and a Glasgow Coma Score of 4 or less cannot be explained by sedation." (NHS ODT)

> "Refer patients for whom a decision has been made to withdraw life-sustaining treatment." (NHS ODT)

> "...parameters indicative of organ donation potential, such as a combination of the patient (1) is on mechanical ventilation, (2) is admitted to the intensive care unit (ICU) because a neurocritical injury, and (3) has a Glasgow Coma Score of ≤5." (Transplantation Direct, 2022)

## Observaciones

- **Variabilidad de triggers (PMC13007231):** distintos sistemas usan umbrales distintos; no hay un único estándar global. Argentina ya eligió ≤7 — conviene respetarlo para no abrir un debate clínico innecesario.
- **DBI pathway (PubMed 31773544):** un protocolo formal de daño cerebral devastador en una UCI de neurociencias mejoró outcomes, uso de recursos y donación a 3 años → respalda que **formalizar el camino de detección temprana funciona**.
- El equivalente argentino del SNOD es el **coordinador hospitalario** (Res. 1642/22) — ver [19_incucai_deteccion_psg7_garantia_calidad.md](19_incucai_deteccion_psg7_garantia_calidad.md).
