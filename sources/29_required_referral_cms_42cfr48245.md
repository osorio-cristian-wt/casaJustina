# "Required referral" (CMS 42 CFR 482.45, USA) — avisar al OPO antes de retirar el soporte vital

- **URL:** https://www.ecfr.gov/current/title-42/chapter-IV/subchapter-G/part-482/subpart-C/section-482.45
- **URL secundaria:** https://www.law.cornell.edu/cfr/text/42/482.45
- **URL secundaria:** https://www.organdonationalliance.org/insight/organ-donation-cms-essentials/
- **URL secundaria:** https://www.lifeshareuniversity.org/uploads/4/0/5/4/40547033/cms_hospital_conditions_of_participation.pdf
- **Fecha de consulta:** 2026-05-31
- **Tipo:** norma/internacional (regulación federal USA)

## Resumen del contenido

Es el **análogo regulatorio exacto de la idea del equipo**: en Estados Unidos, la "Condition of Participation" 42 CFR 482.45 **obliga por ley a todo hospital** a notificar al organismo de procuración (OPO) cada muerte y cada **muerte inminente**, de forma oportuna y —clave— **antes de retirar cualquier terapia de soporte vital**. Es el mecanismo legal que evita que un potencial donante se "pierda" porque alguien lo desconectó sin avisar.

## Información clave

- **"Required referral":** el hospital debe notificar al OPO **toda muerte y toda muerte inminente** ("every death and every imminent death"). No es opcional ni a criterio del médico.
- **Timing:** "as soon as possible (ideally **within one hour**)" después de que el paciente murió, fue **puesto en ventilador por daño cerebral severo** (muerte inminente), o fue **declarado en muerte encefálica**, **Y antes del retiro de cualquier terapia de soporte vital** ("prior to the withdrawal of any life-sustaining therapies").
- **Texto del CFR (núcleo):** el hospital debe "notify, in a timely manner, the OPO ... of individuals whose death is imminent or who have died in the hospital".
- **Acuerdo escrito obligatorio:** Memorandum of Agreement (MOA) entre hospital y su OPO designado, con políticas y procedimientos escritos.
- **Quién decide los criterios clínicos:** **no hay un estándar nacional** de qué criterio de cabecera dispara la notificación; cada hospital sigue la guía de su OPO local (de ahí la variabilidad de triggers — ver [20](20_triggers_clinicos_premortem_nhs_usa.md)).
- Origen: las Conditions of Participation para donación se publicaron en **1998** y hacen al hospital responsable ante CMS por su programa de donación.

## Relevancia para el sistema de trasplantes

Da **respaldo de política pública** a las dos peticiones del equipo: (1) que **el médico sepa que no debe desconectar** sin antes avisar —en USA es ilegal retirar soporte vital antes de notificar al OPO— y (2) que **el coordinador y la logística sean avisados** de toda muerte inminente/declarada. Argentina **no tiene un equivalente tan explícito de "required referral"**, aunque la Ley 27.447 Art. 14 (deber de detección) y Art. 39 (iniciar proceso al certificar) apuntan en la misma dirección ([26](26_marco_legal_deteccion_premortem.md)). El proyecto puede **implementar técnicamente** lo que en USA es obligación legal.

## Implicancias tecnológicas / de diseño

- **Regla "avisar antes de desconectar" como evento del sistema:** si el HIS registra una orden de retiro de soporte vital (WLST) o de muerte encefálica, el sistema debe **garantizar** que la notificación al coordinador ya ocurrió → puede actuar como salvaguarda ("¿se avisó al coordinador antes de este WLST?").
- **Notificación "dentro de 1 hora" como SLA objetivo:** el benchmark de CMS (≤1 h) sirve de meta medible para el piloto argentino.
- **Cobertura total, no muestreo:** el principio de "toda muerte inminente" evita el sesgo de detección — alinea con la cobertura ≥95 % planteada en [analisis/05](../analisis/05_alertas_glasgow_his_hospitalarios.md).
- **Argumento normativo para INCUCAI:** proponer que el sistema ayude a operacionalizar un "required referral a la argentina" (apoyado en Art. 14/39) en lugar de crear una obligación nueva.

## Citas relevantes

> "The hospital must notify, in a timely manner, the OPO or a third party designated by the OPO of individuals whose death is imminent or who have died in the hospital." (42 CFR 482.45)

> "...contact their designated OPO as soon as possible (ideally within one hour) after a patient has died, has been placed on a ventilator due to a severe brain injury ..., or has been declared brain dead, AND prior to the withdrawal of any life sustaining therapies." (Organ Donation Alliance — síntesis de la regla CMS)

## Observaciones

- **Matiz de fetch:** el texto literal del CFR obtenido directamente confirma la parte "timely / imminent death / written protocols"; la frase "prior to the withdrawal of any life-sustaining therapies" proviene de la guía operativa (Organ Donation Alliance / LifeShare) que interpreta la regla CMS. Citar en consecuencia.
- **Diferencia AR/USA a tener presente:** en USA la donación es opt-in (registro) pero el **referral** es obligatorio; en Argentina la donación es opt-out (presunta) pero **no hay required referral explícito**. El proyecto cubre justamente ese hueco operativo.
- Antecedente de impacto de implementarlo: [31](31_cds_muerte_encefalica_inminente_zier.md) y [21](21_automatizacion_referral_ehr_estudio.md).
