# Entrega — Etapa 1: Comprensión del problema

> **Desafío de Innovación Entre Ríos 2026 — AFIDEER**
> **Desafío seleccionado:** *Detección y notificación de potenciales donantes* (propuesto por **Casa Justina**)
>
> Esta entrega cubre los seis puntos exigidos por la consigna de la etapa: descripción del desafío, contexto y problemática, actores involucrados, principales hallazgos de investigación, formulación del problema y supuestos/hipótesis del equipo. **No se presenta una solución desarrollada** — el objetivo de esta etapa es demostrar comprensión, análisis y capacidad de investigación.

---

## Datos de la entrega

| Campo | Detalle |
|---|---|
| **Desafío** | Detección y notificación de potenciales donantes |
| **Sponsor del desafío** | Casa Justina (ONG impulsora de la Ley Justina 27.447) |
| **Consorcio organizador** | AFIDEER (UNER, UADER, UAP, UCA, UTN) |
| **Universidad del equipo** | Universidad Adventista del Plata (UAP) — Entre Ríos |
| **Equipo / integrantes** | _(completar)_ |
| **Fecha de entrega** | 2026-05-31 |
| **Premio del desafío** | Participación en el Congreso AFIDE — Panamá, noviembre 2026 |

> El soporte documental de esta entrega (30 fuentes citadas y 6 análisis críticos) proviene de un **repositorio público dedicado a esta investigación** ([github.com/osorio-cristian-wt/casaJustina](https://github.com/osorio-cristian-wt/casaJustina)), donde el equipo documentó cada fuente en su propio archivo —con URL original, fecha de consulta, resumen y citas literales— para que todo lo afirmado acá sea trazable. Los enlaces de este documento apuntan a ese repositorio. Ver [§7 — Metodología](#7-metodología-de-investigación-del-equipo) y el [índice de fuentes](#índice-de-fuentes-citadas).

---

## 1. Descripción breve del desafío seleccionado

El sistema argentino de trasplantes depende de que, en las terapias intensivas, se **identifique a tiempo a los potenciales donantes** y se **notifique de forma oportuna y confiable** al sistema de procuración. Hoy ese proceso —aunque la ley lo exige— **no se realiza de manera sistemática ni integrada**, y eso se traduce en pérdida de oportunidades de donación y, en última instancia, en vidas que no se salvan.

El desafío propuesto por Casa Justina pregunta:

> **¿Cómo podríamos mejorar la identificación y notificación de potenciales donantes en terapias intensivas, de manera que el sistema de trasplantes reciba esta información de forma oportuna y confiable?**

Es un problema de naturaleza **operacional y cultural** antes que puramente tecnológica: el criterio clínico para detectar y la obligación legal de notificar **ya existen**; lo que falla es su ejecución consistente en cada hospital. Enunciado oficial completo: [sources/00_desafio_casa_justina_enunciado.md](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/00_desafio_casa_justina_enunciado.md).

---

## 2. Contexto y problemática identificada

### 2.1 El contexto: una brecha humana enorme

Argentina trasplanta mucho menos de lo que podría:

| Indicador | Argentina (2024) | España (referencia) |
|---|---|---|
| Donantes por millón de habitantes (DPMH) | **17,7** | 51,9 (2025) |
| Trasplantes realizados | 4.263 | >6.300 (2025) |
| Donantes / procesos de donación | 1.972 | — |
| Personas en lista de espera | ~7.500 (estimado) | — |
| Mortalidad anual en lista de espera | **25–35 %** (estimación, a confirmar) | — |
| Negativa familiar | **~40 %** | ~12 % |
| Donación en asistolia | **~2 %** | ~50 % |

> *Los valores de España (DPMH y trasplantes) son de 2025 y los de Argentina de 2024 — años distintos, comparar con cautela. La lista de espera y la mortalidad en lista son estimaciones que conviene confirmar con INCUCAI. Fuentes: [sources/11](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/11_estadisticas_argentina_2024_2025.md), [sources/13](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/13_modelo_espanol_ont.md).*

> **Dimensión de la oportunidad:** si Argentina alcanzara una DPMH de 30 tendría ~3.300 donantes (~7.000 trasplantes); con la DPMH española (~52), ~5.000 donantes (~10.000 trasplantes) — proyección lineal a partir de las cifras 2024. *Fuentes: [sources/11](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/11_estadisticas_argentina_2024_2025.md), [sources/13](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/13_modelo_espanol_ont.md).*

El marco legal es **habilitante, no restrictivo**: la **Ley 27.447 ("Ley Justina", 2018)** establece el donante presunto para mayores de 18 años, designa al INCUCAI como autoridad de aplicación y —en su Art. 14— obliga a los establecimientos a contar con servicios que permitan "la correcta **detección**, evaluación y tratamiento del donante" ([sources/04](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/04_ley_27447_justina.md), [sources/26](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/26_marco_legal_deteccion_premortem.md)). El problema no es que falte la norma, sino que su cumplimiento operativo es desigual.

### 2.2 La problemática

> *"La detección y notificación de potenciales donantes en terapias intensivas no se realiza de forma sistemática ni integrada, lo que limita la cantidad de trasplantes posibles y reduce las oportunidades de salvar vidas."* — Enunciado oficial.

El propio enunciado descompone las **causas raíz** del problema, y nuestra investigación las confirma y matiza:

| Causa señalada en el enunciado | Lectura del equipo tras la investigación |
|---|---|
| Desconocimiento de la Ley Justina | Hay un componente **cultural/educativo** real; el sponsor (Casa Justina) lo enfatiza. |
| Factores operativos/organizacionales del equipo de UTI | La detección depende de que **un humano lea fichas o planillas**; compite con la carga asistencial. |
| Protocolos institucionales desactualizados | Cada institución tiene autonomía; muchos protocolos no se actualizaron a la Ley Justina. |
| Falta de recursos adecuados | Sistemas que **no se hablan entre sí** y notificación manual (teléfono/WhatsApp en cascada). |
| Prioridades del equipo de terapia intensiva | El objetivo asistencial inmediato desplaza la tarea de detección/notificación. |

Dos atributos exigidos por el enunciado —**"oportuna"** y **"confiable"**— son medibles: oportuna = baja latencia entre el evento clínico y el aviso; confiable = repetible, auditable e integrada con los sistemas existentes (SINTRA, HIS hospitalarios). Esto nos da, desde el inicio, un criterio cuantitativo para evaluar cualquier solución futura.

---

## 3. Actores involucrados

El enunciado nombra cinco grupos de actores; la investigación los desagrega en el ecosistema real de procuración (mapa completo en [research.md §1](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md)).

| Actor (categoría del enunciado) | Quién es en concreto | Rol en el problema |
|---|---|---|
| **Equipos de terapia intensiva** | Intensivistas y enfermería de UTI | Primer punto de detección (registran el Glasgow, evalúan al paciente neurocrítico). |
| **Personal de salud** | Coordinadores hospitalarios de donación (~130 en el país) | Eslabón clave: deben recibir el aviso y seguir al potencial donante **sin intervenir en su cuidado**. |
| **Sistema de trasplantes** | **INCUCAI** (autoridad nacional) + **SINTRA** (sistema central, stack 2003) | Recibe la notificación, asigna órganos (única etapa automatizada del proceso). |
| **Sistema de trasplantes (nivel provincial)** | **24 CUCAI/COTAI** provinciales + **28 UHPROT** | Operan jurisdiccionalmente con autonomía; coordinan el operativo. |
| **Instituciones de salud** | Hospitales procuradores y centros de trasplante | Deben tener protocolos y servicios de detección actualizados (Art. 14). |
| **Organismos regulatorios** | Ministerio de Salud, COFETRA, AAIP (datos personales) | Marco normativo, garantía de calidad, protección de datos. |
| **Sponsor / sociedad civil** | **Casa Justina** (+ alianza con H+Trace en drones) | Impulsa la innovación, valida clínicamente y abre puertas institucionales. |

*Fuentes: [sources/01](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/01_incucai_argentina_gob.md), [sources/02](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/02_sintra_modulos_arquitectura.md), [sources/12](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/12_casa_justina_drones_htrace.md), [research.md §1](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md).*

---

## 4. Principales hallazgos de investigación

Resultado de **30 fuentes documentadas** y **6 análisis críticos**. Se ordenan de lo más estructural a lo más específico.

### H‑A — El criterio de detección **ya está normado**; el cuello es de *ejecución*, no de *definición*

El **Subprograma de Garantía de Calidad del INCUCAI** se basa explícitamente en detectar y monitorear pacientes neurocríticos con **Glasgow ≤ 7 (PSG<7)** ([sources/19](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/19_incucai_deteccion_psg7_garantia_calidad.md)), y la Ley 27.447 Art. 14 obliga a detectar. El gap es que ese monitoreo **hoy se hace a mano**. Esto es decisivo: automatizarlo no inventa una práctica nueva, sino que **ejecuta lo que la norma ya pide** → baja el riesgo regulatorio.

### H‑B — Existe evidencia internacional fuerte de que automatizar la detección funciona

Automatizar la derivación por *triggers* desde la historia clínica electrónica (ventilación + UCI neurocrítica + Glasgow bajo) aumentó los **donantes efectivos +92 %** (referrals +45 %, autorizaciones +73 %) en hospitales piloto de Texas ([sources/21](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/21_automatizacion_referral_ehr_estudio.md)). Un sistema de soporte a la decisión (CDS) bajó el tiempo de notificación de **30,2 h a 1,7 h** ([sources/31](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/31_cds_muerte_encefalica_inminente_zier.md)).

### H‑C — El proceso tiene **dos eventos-disparador**, no uno

| Etapa | Evento disparador | Qué debe pasar | Quién decide |
|---|---|---|---|
| **1 — Detección pre-mortem** | Glasgow ≤ 7 en paciente neurocrítico | **Vigilar**: avisar al coordinador para que siga al paciente | La detección puede automatizarse; certificar la muerte es humano |
| **2 — Muerte encefálica** | Certificación según Res. 716/2019 | **Disparar logística** (coordinador + CUCAI + equipos) y **mantener al donante (no desconectar)** | Certificación y diálogo con la familia siguen siendo **humanos** |

La muerte encefálica es la **frontera legal**: a partir de ahí opera la presunción de donación (Art. 33, sin veto familiar para adultos — [sources/04](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/04_ley_27447_justina.md)) y el Art. 39 ("Notificación") obliga a iniciar el proceso. Análisis completos: [analisis/05](https://github.com/osorio-cristian-wt/casaJustina/blob/main/analisis/05_alertas_glasgow_his_hospitalarios.md) (etapa 1) y [analisis/06](https://github.com/osorio-cristian-wt/casaJustina/blob/main/analisis/06_disparo_logistica_muerte_encefalica.md) (etapa 2).

### H‑D — Detalles técnicos que condicionan cualquier solución

- **El Glasgow se lee del HIS/HCE, no del monitor de cabecera:** es una valoración asistencial que carga enfermería → la integración debe ser vía **FHIR sobre el HIS**, no leyendo el monitor ([sources/23](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/23_monitores_uti_captura_datos.md)).
- **El destinatario correcto de la alerta es el coordinador de donación, no el intensivista:** alertar al médico de cabecera genera *alert fatigue* y *override* habitual; al coordinador le llega como su propia lista de trabajo ([sources/25](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/25_alert_fatigue_cds_uti.md)).
- **"No desconectar" es un problema clínico real:** tras la muerte encefálica, hasta el **20 % de los órganos se pierde por mal manejo hemodinámico** del donante; el aviso debe traer las metas de mantenimiento, no ser solo administrativo ([sources/30](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/30_manejo_donante_muerte_encefalica_no_desconectar.md)).

### H‑E — SINTRA es un sistema cerrado, pero la ley no lo impide abrir

**SINTRA** (sistema central del INCUCAI) corre sobre stack de **2003** (Linux + Oracle + J2EE), tiene 6 módulos y **no expone APIs públicas ni soporte FHIR/HL7** ([sources/02](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/02_sintra_modulos_arquitectura.md)). Es el sistema **más cerrado** comparado con UNOS (238 APIs), NHS BT o Eurotransplant ([sources/09](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/09_comparacion_internacional_unos_eurotransplant_nhs.md)). Sin embargo, la normativa **no prohíbe** APIs ni integración FHIR: la Res. MS 680/2018 ya adoptó **FHIR R4 + SNOMED CT** como estándares nacionales de salud digital ([sources/08](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/08_interoperabilidad_fhir_argentina.md)). La estrategia viable es **"complemento, no reemplazo"**.

### H‑F — Hay infraestructura pública que vuelve esto escalable

El **HSI (Historia de Salud Integrada)** es un HIS público, open source, presente en **+1.800 efectores** ([sources/24](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/24_hsi_historia_salud_integrada.md)). Una integración "1‑a‑muchos" sobre HSI permitiría escalar sin negociar hospital por hospital. **HIBA (Hospital Italiano)** —primer hospital del país en alcanzar **HIMSS EMRAM 7**, con 25+ años en informática en salud y FHIR desde 2014— es el candidato natural a piloto técnico ([sources/16](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/16_hospital_italiano_hiba_fhir.md)).

### H‑G — Existe precedente de innovación público-privada exitosa

**Casa Justina + H+Trace** ya operan drones autorizados por ANAC para transporte de órganos, reduciendo la última milla urbana de 1–2,5 h a <30 min y **8× más barato** ([sources/12](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/12_casa_justina_drones_htrace.md)). Demuestra que el ecosistema **acepta innovación** cuando se canaliza por el sponsor adecuado.

---

## 5. Formulación clara del problema

> **Problema central:** En las terapias intensivas argentinas, la **detección de potenciales donantes** (paciente neurocrítico con Glasgow ≤ 7) y la **notificación al sistema de procuración** (coordinador hospitalario → CUCAI → INCUCAI) ocurren de forma **manual, dependiente de la iniciativa individual y no integrada con los sistemas clínicos**. Esto produce **detecciones tardías o ausentes** y **notificaciones lentas**, que se traducen en **pérdida de potenciales donantes** y, por lo tanto, de trasplantes.

Descompuesto en sub-problemas accionables y medibles:

| # | Sub-problema | Atributo del enunciado que viola | Métrica para evaluarlo |
|---|---|---|---|
| **P1** | Subdetección: el Glasgow ≤ 7 no se monitorea de forma sistemática | No es **sistemática** | % de pacientes elegibles efectivamente detectados |
| **P2** | Notificación lenta y manual al certificarse la muerte encefálica | No es **oportuna** | Latencia evento clínico → aviso (objetivo de referencia: ≤ 1 h) |
| **P3** | Falta de integración entre HIS, SINTRA y los actores | No es **integrada** / **confiable** | Nº de sistemas que se "hablan" vs. recarga manual |
| **P4** | Mal manejo del donante por desconocimiento ("desconectan") | Reduce el **aprovechamiento** | % de órganos perdidos por manejo hemodinámico |
| **P5** | Desconocimiento de la Ley Justina y protocolos desactualizados | Causa raíz **cultural** | Adherencia institucional auditada |

> **Frontera ética y legal del problema (límite autoimpuesto):** la herramienta a diseñar **detecta y avisa al referente de donación**; **no** decide certificar la muerte, **no** dispara la conversación con la familia y **no** interviene en el cuidado del paciente. Ese desacople (*decoupling*) es lo que vuelve la detección pre-mortem **legal y clínicamente aceptable** ([research.md §13, patrón 8](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md)).

---

## 6. Supuestos e hipótesis identificadas por el equipo

### 6.1 Supuestos de trabajo (a validar en etapas siguientes)

| # | Supuesto | Cómo lo validaríamos |
|---|---|---|
| S1 | El Glasgow se registra de forma **estructurada** (no solo texto libre) en los HIS argentinos | Inspección técnica del HSI/HIS + entrevista a coordinadores |
| S2 | El HSI soporta lectura vía **FHIR** (y ojalá `Subscription`/webhooks) | Verificación de versión FHIR (R4/R4B) de HSI |
| S3 | El cuello de botella es de **ejecución**, no de criterio clínico | Confirmado por norma ([sources/19](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/19_incucai_deteccion_psg7_garantia_calidad.md)); falta dato de frecuencia real de registro |
| S4 | Casa Justina puede **abrir puertas** ante INCUCAI/RENAPER | Precedente de drones ([sources/12](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/12_casa_justina_drones_htrace.md)) |
| S5 | Se puede operar **sin tocar SINTRA**, como capa de orquestación | Arquitectura conceptual en [research.md §10](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md) |

### 6.2 Hipótesis estratégicas

| # | Hipótesis | Fundamento |
|---|---|---|
| **H1** | La principal palanca de impacto **no es tecnológica**: mejorar detección + bajar la negativa familiar de 40 % a 20 % aumentaría los trasplantes ~50 % sin tocar SINTRA. La tecnología es *enabler* del cambio operativo. | [research.md §11](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md), [sources/13](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/13_modelo_espanol_ont.md) |
| **H2** | La capa más valiosa a construir es la de **orquestación / coordinación en tiempo real**, no un nuevo registro. SINTRA ya hace bien el matching. | [research.md §11](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md) |
| **H3** | **Casa Justina es el partner natural**: reconocimiento institucional, proyecto innovador activo y acceso a hospitales e INCUCAI. | [sources/12](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/12_casa_justina_drones_htrace.md) |
| **H4** | **HIBA (Hospital Italiano) es el partner técnico natural** para un piloto FHIR de bajo riesgo. | [sources/16](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/16_hospital_italiano_hiba_fhir.md) |
| **H5** | Automatizar la detección/notificación **respetando el desacople** es legal con el marco vigente (Art. 14/39), sin necesidad de norma nueva. | [sources/26](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/26_marco_legal_deteccion_premortem.md), [analisis/06](https://github.com/osorio-cristian-wt/casaJustina/blob/main/analisis/06_disparo_logistica_muerte_encefalica.md) |
| **H6** | El **gap atacable más concreto y de mayor evidencia** es la subdetección de Glasgow ≤ 7 desde el HIS (evidencia: +92 % donantes). | [sources/21](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/21_automatizacion_referral_ehr_estudio.md), [analisis/05](https://github.com/osorio-cristian-wt/casaJustina/blob/main/analisis/05_alertas_glasgow_his_hospitalarios.md) |

---

## 7. Metodología de investigación del equipo

Para demostrar **capacidad de investigación**, el equipo siguió una metodología replicable y documentada (detallada en [WORKFLOW.md](https://github.com/osorio-cristian-wt/casaJustina/blob/main/WORKFLOW.md)):

```
RECONNAISSANCE → DEEP DIVE → SYNTHESIS → CRITICAL ANALYSIS → DECISIONES
(búsquedas      (1 archivo    (research.md  (1 análisis        (README +
 amplias)        por fuente)   maestro)      por propuesta)     next steps)
```

- **Principio rector:** *una fuente → un archivo*; toda afirmación de síntesis es trazable a una fuente.
- **Resultado tangible:** **30 fuentes** documentadas con URL, fecha de consulta y citas literales; **6 análisis críticos** con plan, costos y riesgos; un documento maestro ([research.md](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md)) con 16 secciones; y un [glosario de siglas](https://github.com/osorio-cristian-wt/casaJustina/blob/main/GLOSARIO.md).
- **Trazabilidad total:** cada hallazgo de esta entrega enlaza a su fuente. Nada se afirma sin respaldo.

---

## Índice de fuentes citadas

Cada enlace de abajo abre el archivo correspondiente en el **repositorio de investigación del equipo** en GitHub ([github.com/osorio-cristian-wt/casaJustina](https://github.com/osorio-cristian-wt/casaJustina)), donde la fuente está documentada con su URL original, fecha de consulta, resumen e implicancias. Esta es una selección de las que sostienen la entrega; el índice completo de fuentes está en [research.md §15](https://github.com/osorio-cristian-wt/casaJustina/blob/main/research.md).

| # | Fuente | Aporte a esta entrega |
|---|---|---|
| 00 | [Enunciado oficial del desafío](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/00_desafio_casa_justina_enunciado.md) | Desafío, contexto, actores |
| 02 | [SINTRA — módulos y arquitectura](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/02_sintra_modulos_arquitectura.md) | Hallazgo H‑E (sistema cerrado) |
| 04 | [Ley 27.447 (Justina) — completa](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/04_ley_27447_justina.md) | Marco legal: donante presunto (Art. 33), detección (Art. 14), notificación (Art. 39) |
| 09 | [Comparación internacional](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/09_comparacion_internacional_unos_eurotransplant_nhs.md) | Benchmark de apertura de APIs |
| 11 | [Estadísticas Argentina 2024](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/11_estadisticas_argentina_2024_2025.md) | Datos duros del contexto |
| 12 | [Casa Justina + drones H+Trace](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/12_casa_justina_drones_htrace.md) | Precedente de innovación |
| 13 | [Modelo español (ONT)](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/13_modelo_espanol_ont.md) | Referencia de DPMH |
| 19 | [Detección PSG<7 + Garantía de Calidad](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/19_incucai_deteccion_psg7_garantia_calidad.md) | Hallazgo H‑A (criterio ya normado) |
| 21 | [Automatización de referral (EHR)](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/21_automatizacion_referral_ehr_estudio.md) | Evidencia +92 % donantes |
| 23 | [Monitores UTI / captura de datos](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/23_monitores_uti_captura_datos.md) | El Glasgow se lee del HIS |
| 24 | [HSI — Historia de Salud Integrada](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/24_hsi_historia_salud_integrada.md) | Escala 1‑a‑muchos |
| 25 | [Alert fatigue en CDS](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/25_alert_fatigue_cds_uti.md) | Destinatario correcto de la alerta |
| 26 | [Marco legal detección pre-mortem](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/26_marco_legal_deteccion_premortem.md) | Legalidad del desacople |
| 30 | [Manejo del donante — no desconectar](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/30_manejo_donante_muerte_encefalica_no_desconectar.md) | Hallazgo H‑D (−20 % órganos) |
| 31 | [CDS muerte encefálica (Zier)](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/31_cds_muerte_encefalica_inminente_zier.md) | Evidencia 30,2 h → 1,7 h |

**Análisis críticos relacionados:** [analisis/05 — Alertas Glasgow desde HIS](https://github.com/osorio-cristian-wt/casaJustina/blob/main/analisis/05_alertas_glasgow_his_hospitalarios.md) · [analisis/06 — Disparo de logística al certificarse la muerte encefálica](https://github.com/osorio-cristian-wt/casaJustina/blob/main/analisis/06_disparo_logistica_muerte_encefalica.md).

---

> **Nota de cierre.** En línea con la consigna, esta etapa **no presenta una solución desarrollada**. El espacio de soluciones (detectar / identificar / notificar / capacitar / cumplir) está mapeado en [sources/00](https://github.com/osorio-cristian-wt/casaJustina/blob/main/sources/00_desafio_casa_justina_enunciado.md) y será el punto de partida de la siguiente etapa del Desafío AFIDEER 2026.
