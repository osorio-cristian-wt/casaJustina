# Alert fatigue — cómo diseñar la alerta de detección para que NO se ignore

- **URL:** https://pmc.ncbi.nlm.nih.gov/articles/PMC9132737/ (CDS Stewardship — best practices)
- **URL secundaria:** https://academic.oup.com/jamiaopen/article/6/3/ooad056/7235064 (Optimización BPA, Singapur — reducción 59 %)
- **URL secundaria:** https://pmc.ncbi.nlm.nih.gov/articles/PMC10830237/ (reemplazar alerta interruptiva por CDS pasivo)
- **URL secundaria:** https://pmc.ncbi.nlm.nih.gov/articles/PMC10461946/ (facilitadores/barreras de CDS en UTI)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** académico (informática biomédica / JAMIA)

## Resumen del contenido

Literatura sobre **fatiga de alertas**: cuando un sistema de soporte a la decisión (CDS) genera demasiadas alertas, los clínicos las ignoran por hábito y la herramienta deja de funcionar. Es el principal riesgo de fracaso de un sistema de alertas PSG<7, sobre todo porque el umbral argentino (≤7) es el más sensible (más casos → más alertas) de los comparados ([20_triggers_clinicos_premortem_nhs_usa.md](20_triggers_clinicos_premortem_nhs_usa.md)).

## Información clave

- **Causa de la fatiga:** baja tasa de aceptación por mal targeting del destinatario, información incorrecta, guía no accionable o desalineada con el flujo de trabajo → desconfianza y **override habitual** (rechazo automático sin leer).
- **Las alertas "open-chart"** (saltan al abrir la historia del paciente) son especialmente problemáticas: aparecen "en el momento equivocado" del flujo.
- **CDS Five Rights** (framework guía): la información correcta, a la persona correcta, en el formato correcto, por el canal correcto, en el momento correcto del flujo de trabajo.
- **Gobernanza + revisión de datos** de las alertas logró hasta **−59 % de alertas interruptivas** y aumento de la tasa de acción (estudio Singapur).
- **CDS pasivo > interruptivo** en muchos casos: en vez de un pop-up que bloquea, mostrar la señal de forma no intrusiva (un indicador, una lista de trabajo) reduce fatiga sin perder la información.
- **En UTI la aceptación de CDS baja cuanto más complejo es el paciente**, y el entorno es de alto estrés → diseño aún más cuidadoso.
- **Reducir repeticiones dentro del mismo paciente** es un objetivo de alto rendimiento contra el override.

## Relevancia para el sistema de trasplantes

Decide si el sistema **funciona o se vuelve ruido**. Una alerta PSG<7 mal diseñada (pop-up repetitivo al médico de UTI cada vez que abre la HC) sería ignorada en semanas. La estrategia ganadora, alineada con la idea del equipo, es **no interrumpir al intensivista**: enrutar la señal al **coordinador hospitalario** (la "persona encargada de donantes"), por un canal propio, como una **lista de pacientes a vigilar**, no como un bloqueo en el flujo clínico de quien atiende al paciente.

## Implicancias tecnológicas

- **Destinatario correcto = coordinador hospitalario, no el médico de cabecera.** Esto resuelve de raíz la fatiga: el clínico de UTI no recibe alertas extra; el coordinador recibe una lista de trabajo que es exactamente su tarea (detección).
- **Canal separado del HIS:** app/dashboard/notificación push del coordinador, no un pop-up dentro de la HC.
- **Deduplicación obligatoria:** una sola entrada por paciente-episodio, con actualización de estado — no una alerta por cada registro de Glasgow ≤7.
- **CDS pasivo:** "worklist" de potenciales donantes que el coordinador revisa, con priorización, en lugar de interrupciones.
- **Gobernanza desde el día 1:** medir tasa de acción, falsos positivos y override; ajustar umbral/filtros con datos (Five Rights).
- **Accionabilidad:** cada alerta debe traer el "qué hacer ahora" (evaluar elegibilidad, contactar UTI), no sólo "Glasgow bajo".

## Citas relevantes

> "Low acceptance rates may result from poor targeting of recipients, incorrect information, un-actionable guidance, or misaligned workflow and contribute to alert distrust and alert fatigue." (CDS Stewardship, PMC9132737)

> "...an average of 59% reduction in interruptive alerts across all clinical groups." (JAMIA Open, Singapur)

> "CDSS acceptance in the form of best practice advisories are lower with increasing patient complexity, and the ICU is a stressful environment for clinicians." (PMC10461946)

## Observaciones

- Conecta directamente con la limitación #5 del estudio de Texas (personal del OPO preocupado por **sobrecarga de derivaciones**): [21_automatizacion_referral_ehr_estudio.md](21_automatizacion_referral_ehr_estudio.md).
- **Diseño recomendado para el proyecto:** alerta → coordinador (no intensivista), canal propio, worklist pasiva, deduplicada, accionable, con métricas de gobernanza.
- **A verificar:** carga de trabajo real de un coordinador hospitalario argentino (cuántos pacientes puede vigilar) para dimensionar el volumen tolerable de alertas.
