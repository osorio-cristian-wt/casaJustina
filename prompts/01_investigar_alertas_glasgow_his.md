# Prompt — Investigar "Alertas Glasgow ≤7 desde HIS hospitalarios"

> Pegá este prompt completo en una sesión nueva de Claude (o cualquier LLM con WebSearch/WebFetch). Está diseñado para que reproduzca el workflow del repo sin necesidad de explicarle más contexto.

---

## PROMPT (copiar desde aquí ↓)

```
Vas a continuar la investigación del repositorio `f:\repos\casaJustina\` siguiendo la metodología definida en `WORKFLOW.md`.

CONTEXTO DEL PROYECTO
---------------------
Es un repo de investigación para el Desafío de Innovación Entre Ríos 2026 (AFIDEER) cuyo sponsor es Casa Justina. El desafío oficial es "Detección y notificación de potenciales donantes" en terapias intensivas (enunciado completo en `sources/00_desafio_casa_justina_enunciado.md`). El equipo es de la Universidad Adventista del Plata (UAP).

El repo ya tiene 19 fuentes documentadas (`sources/`), 3 análisis críticos sobre RENAPER (`analisis/`), un research.md maestro y un GLOSARIO.md. Antes de hacer nada leé estos archivos en este orden:
1. README.md
2. WORKFLOW.md (especialmente secciones 1–7)
3. sources/00_desafio_casa_justina_enunciado.md
4. research.md secciones 8 y 9 (cuellos de botella y oportunidades)
5. sources/14_cuellos_botella_operacionales.md
6. sources/08_interoperabilidad_fhir_argentina.md
7. sources/16_hospital_italiano_hiba_fhir.md
8. GLOSARIO.md (siglas)

TAREA
-----
Investigar la línea "Alertas Glasgow ≤7 desde HIS hospitalarios" — uno de los vectores pendientes listados en WORKFLOW.md § 9. El objetivo final es producir:
- 3 a 6 archivos nuevos en `sources/` (numerados desde 19 en adelante).
- 1 archivo nuevo en `analisis/` (numerado 04) con análisis crítico de viabilidad.
- Actualizar `research.md` con cualquier hallazgo transversal.
- Actualizar `README.md` § "Estado actual" marcando este vector como completo.

PREGUNTAS A RESOLVER
--------------------
1. ¿Qué HIS (Hospital Information Systems) usan los hospitales argentinos? Mapear al menos: HSI (Provincia BA), SISA Nacional, sistemas privados grandes (Hospital Italiano HIBA, Hospital Austral, Fundación Favaloro, Sanatorio Allende, Garrahan), sistemas comerciales (eRecord/Plenario, GeoSalud, Citrix Health, ABCMS, etc).
2. ¿Cuáles de estos HIS soportan webhooks, FHIR Subscription, o eventos en tiempo real? ¿Cuáles solo permiten polling?
3. ¿Cómo está implementado HOY el "subprograma de garantía de calidad" de INCUCAI que monitorea pacientes con Glasgow ≤7? Ver Programa Federal de Procuración.
4. ¿Los monitores de signos vitales de UTI (Philips IntelliVue, GE Carescape, Mindray BeneVision, Drager Infinity) exportan datos? ¿Vía HL7 v2, FHIR, propietario? ¿Se puede leer el Glasgow Coma Scale en tiempo real?
5. ¿Cómo se calcula y registra el Glasgow en la práctica argentina? ¿Lo carga el enfermero a mano cada turno, lo trae el monitor, hay scoring automatizado?
6. Casos internacionales similares: NHS UK Sepsis alerts, USA Epic BPA (Best Practice Advisories), Israel Sheba Beacon, India AIIMS alerts. ¿Qué arquitecturas usan?
7. Riesgo de "alert fatigue" — qué dicen los papers sobre saturación de alertas en UTI y cómo evitarla.
8. Marco legal argentino: ¿alertar a un coordinador hospitalario con datos del paciente cumple Ley 26.529 (derechos del paciente), Ley 25.326 (datos personales), Ley 26.061 si es pediátrico?
9. Arquitecturas alternativas: caja IoT que escucha del monitor sin pasar por HIS ("HIS bypass"); cámara que detecta pupilas dilatadas con AI; smart UTI room.
10. Estimación de impacto: si el 50% de las UTI argentinas tuvieran este sistema, ¿cuántos donantes adicionales por año? Modelar con datos del research.md (DPMH actual vs potencial).

WORKFLOW A SEGUIR
-----------------
Fase 1 — Reconnaissance (broad):
- Hacé 6–8 búsquedas web EN PARALELO usando la misma llamada de mensaje (no secuencial). Cubrí los ángulos: HIS argentinos, FHIR Subscription, monitores Philips/GE/Mindray, Epic BPA Glasgow, NHS Sepsis, alert fatigue, INCUCAI subprograma calidad, HSI PBA.
- NO escribas archivos todavía. Devolveme la lista de URLs prometedoras con un comentario breve por cada una.

Fase 2 — Deep dive:
- Por cada URL realmente útil, hacé WebFetch con un prompt extractor específico (datos, arquitectura, actores citados, citas literales).
- Para cada fuente escribí un archivo `sources/NN_*.md` siguiendo la plantilla de WORKFLOW.md § 2 — campos obligatorios: URL, fecha de consulta, tipo, resumen, info clave, relevancia, implicancias tecnológicas, citas literales, observaciones.
- NN empieza en 19 y se incrementa por cada fuente. Nombre del archivo en snake_case corto y descriptivo.

Fase 3 — Synthesis:
- Actualizá research.md, específicamente las secciones 8 (cuellos de botella) y 9 (oportunidades). Sumá ítems nuevos sin romper la estructura existente.
- Si encontrás patrones nuevos (ej: "todos los HIS argentinos carecen de FHIR Subscription productivo"), agregalo a § 13 "Patrones detectados".
- Si encontrás contradicciones con lo ya escrito, márcalas en § 12 "Inconsistencias y datos a verificar".

Fase 4 — Análisis crítico:
- Crear `analisis/04_alertas_glasgow_his_hospitalarios.md` siguiendo la plantilla de WORKFLOW.md § 3.
- Secciones obligatorias: TL;DR, encuadre, capacidades técnicas, arquitectura propuesta, análisis seguridad, análisis legal, contactos / canales, plan de implementación, costos en USD, riesgos, métricas de éxito, ficheros relacionados.
- En "arquitectura propuesta" plantear AL MENOS 3 alternativas (por ejemplo: FHIR Subscription sobre HIS, polling sobre HIS, IoT bypass de HIS) con tabla comparativa.
- En "costos" estimar 3 tiers: prototipo, piloto, producción 5 hospitales. Misma estructura que `analisis/03_sensor_huella_costo_bom.md`.

Fase 5 — Cierre:
- Actualizar README.md: tabla "Estado actual" → marcar este vector como completo, sumar links a los archivos nuevos.
- Si surgió alguna sigla nueva, agregarla a GLOSARIO.md en la sección alfabética correcta.
- Devolveme un resumen ejecutivo de 10–15 líneas con los hallazgos más importantes y la próxima decisión que el equipo tiene que tomar.

REGLAS NO NEGOCIABLES (anti-patrones de WORKFLOW.md § 11)
---------------------------------------------------------
- Toda afirmación debe poder rastrearse a una fuente con URL.
- Datos cuantitativos: número + unidad + año.
- Costos: USD reales con citación, no rangos vagos.
- Tablas con título y columnas no ambiguas.
- Si dudás de un dato, marcalo "estimación" o "a verificar", no afirmes falso.
- NO mezcles varios temas en una sola fuente.
- NO inventes URLs ni cifras — si no encontrás dato, dejá el placeholder explícito.

ESTILO
------
- Español (Argentina).
- Markdown plano, sin imágenes externas.
- Tablas siempre que se pueda en lugar de prosa.
- Tono técnico-operacional, no marketing.
- Citas textuales entre `> "..."`.

EMPEZÁ POR FASE 1 (reconnaissance). Mostrame las búsquedas y URLs antes de avanzar a Fase 2.
```

---

## Cómo usar este prompt

### Opción A — Sesión nueva de Claude Code en este repo
1. Abrí una sesión nueva.
2. Pegá el bloque entre triple backticks.
3. Claude leerá los archivos del repo, hará las búsquedas y producirá los archivos siguiendo el workflow.

### Opción B — Claude.ai web (sin acceso a archivos locales)
1. Antes de pegar el prompt, subí como contexto: `README.md`, `WORKFLOW.md`, `sources/00_*.md`, `research.md`, `GLOSARIO.md`.
2. Pegá el prompt.
3. Al final Claude devolverá los `*.md` listos para que vos los pegues en el repo manualmente.

### Opción C — Otro LLM con WebSearch (ChatGPT, Gemini)
1. Subí los archivos como contexto (similar a B).
2. Pegá el prompt.
3. Posiblemente requiera prompting adicional para forzar el uso de WebSearch en paralelo (no todos los LLMs lo hacen por defecto).

---

## Ajustes posibles (si querés cambiar el foco)

### Si querés que se concentre solo en HIS argentinos
Reemplazá la pregunta 1 y 2 por:
> Mapear y profundizar EN DETALLE los 5 HIS más usados en hospitales públicos argentinos: HSI (PBA), SISA, GeoSalud, y otros 2 a determinar. Para cada uno: arquitectura, capacidades de eventos, casos de uso documentados, contactos institucionales.

### Si querés priorizar viabilidad de implementación
Reforzá la Fase 4 con:
> En análisis crítico, ponderar fuertemente cuál de las 3 arquitecturas es más realista en ≤ 6 meses (deadline AFIDEER nov 2026). El veredicto del TL;DR debe ser una recomendación clara y única, no varias opciones.

### Si querés que también haga prototipo
Sumá después de Fase 5:
> Fase 6 — Prototipo:
> - Generar carpeta `prototipos/04_glasgow_alert/` con: una app Node.js minimal que simule recibir un webhook FHIR Observation, parsea el LOINC code 9269-2 (Glasgow Coma Scale), si valor ≤ 7 emite alerta a un endpoint mock. README, package.json, código.

---

## Iteraciones futuras

Cuando termines este vector, replicá el mismo formato con los otros pendientes del README:

- `prompts/02_investigar_notificacion_realtime.md`
- `prompts/03_investigar_capacitacion_microlearning.md`
- `prompts/04_investigar_dashboard_cumplimiento.md`
- `prompts/05_investigar_modelo_financiamiento.md`

Cada uno con la misma estructura: contexto + tarea + preguntas + workflow + reglas + estilo.
