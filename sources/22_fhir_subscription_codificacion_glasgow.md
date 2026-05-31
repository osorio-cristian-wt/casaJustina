# Mecánica del adaptador — FHIR Subscription (R4B/R5) y codificación del Glasgow

- **URL:** https://www.hl7.org/fhir/R5/subscription.html
- **URL secundaria:** https://build.fhir.org/ig/HL7/fhir-subscription-backport-ig/components.html (Subscriptions R5 Backport para R4)
- **URL secundaria:** https://loinc.org/9269-2 (Glasgow coma score total)
- **URL secundaria:** https://www.hl7.org/fhir/observation-example-glasgow-qa.html (ejemplo FHIR de Observation Glasgow)
- **URL secundaria:** https://github.com/athenahealth/aone-fhir-subscriptions (implementación real de webhook FHIR Subscriptions)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** norma/técnico (HL7 FHIR + LOINC)

## Resumen del contenido

Documenta **cómo, técnicamente, un HIS puede "empujar" un evento clínico hacia el adaptador del proyecto** en tiempo real, y **con qué código se identifica el Glasgow** dentro del estándar que Argentina ya adoptó (FHIR + SNOMED/LOINC, Res. MS 680/2018). Esta es la pieza que vuelve concreta la frase "adaptador de sistemas de historias clínicas".

## Información clave

### FHIR Subscription — el mecanismo de push

| Versión | Qué ofrece | Estado |
|---|---|---|
| **R4 (Subscription clásica)** | Criterio de búsqueda + canal; el servidor notifica cuando un recurso matchea. | Producción, pero criterio rígido. |
| **R4B / R5 Backport IG** | Introduce **`SubscriptionTopic`** (temas predefinidos) backporteado a R4 → permite suscribirse a eventos tipados sin estar en R5. | Recomendado para HIS en R4. |
| **R5 (topic-based)** | Framework de suscripción reescrito alrededor de `SubscriptionTopic` + `Subscription.filterBy`. | Estándar moderno. |

- **Canales de entrega (`Subscription.channel`):** `rest-hook` (**webhook HTTP** — el relevante), `email`, `sms`, `message` (cola).
- **Payload:** puede ser `id-only` (solo manda el ID del recurso; el adaptador después hace fetch), `full-resource`, o vacío. `id-only` es el más conservador en datos (minimización).
- **Filtrado:** `SubscriptionTopic.canFilterBy` define filtros permitidos; `Subscription.filterBy` los aplica (p. ej. "Observation con código 9269-2 y valor ≤ 7").
- **Ejemplo real de implementación:** athenahealth expone su "Event Subscription Platform" conforme al **R5 Backport STU 1.0.0**, soportando canal `rest-hook` y payload `id-only` → prueba de que el patrón es productivo en EHRs comerciales.

### Codificación del Glasgow (lo que el filtro debe mirar)

| Concepto | Código | Sistema |
|---|---|---|
| **Glasgow coma score total** | **9269-2** | LOINC |
| Glasgow — apertura ocular | 11324-1 / 9267-6 | LOINC |
| Glasgow — respuesta motora | 9268-4 / 11325-8 | LOINC |
| Glasgow — respuesta verbal | 9270-0 / 11326-6 | LOINC |

- FHIR publica un ejemplo canónico (`Observation-example-glasgow`) que usa **LOINC 9269-2** como `Observation.code` y los tres componentes como `Observation.component`.
- LOINC tiene mapeo a **SNOMED CT** (terminología que HSI y la Red Nacional usan).

### Patrón de integración resultante

```
HIS / HCE (FHIR R4B)
   │  Observation { code: LOINC 9269-2, value: 6 }  ← enfermería registra Glasgow
   ▼
SubscriptionTopic "deteccion-donante"  (filterBy: code=9269-2 AND value<=7
                                         AND encounter.class=UTI AND ventilado)
   │  rest-hook (webhook HTTPS, payload id-only)
   ▼
ADAPTADOR del proyecto  ── verifica criterio, deduplica, anonimiza ──►  alerta al COORDINADOR
```

## Relevancia para el sistema de trasplantes

Es la respuesta técnica a "ver posibles donantes desde el HIS": **si el HIS habla FHIR R4B+ con Subscription, el adaptador no hace polling ni lee la base de datos del hospital — el HIS le avisa solo**, vía webhook, cuando aparece un Glasgow ≤7. Y como Argentina ya estandarizó FHIR (Res. 680/2018) y HSI lo implementa ([24_hsi_historia_salud_integrada.md](24_hsi_historia_salud_integrada.md)), el camino normativo está allanado.

## Implicancias tecnológicas

- **Si el HIS soporta Subscription → arquitectura push (ideal):** baja latencia, sin carga sobre el HIS, sin acceso directo a su base.
- **Si NO la soporta → fallback polling:** el adaptador consulta periódicamente `GET /Observation?code=9269-2` (más carga, más latencia, pero universal). La mayoría de los HIS argentinos probablemente requieran este fallback hoy.
- **Si el HIS no expone FHIR → bridge HL7 v2:** muchos sistemas mandan mensajes `ORU^R01` (resultados/observaciones) por HL7 v2.x; el adaptador puede escuchar esos mensajes y traducirlos a FHIR internamente.
- **`id-only` + fetch sobre TLS = minimización de datos:** la alerta inicial puede no contener datos identificables, sólo "hay un caso" → mitiga el riesgo de Ley 25.326 ([26_marco_legal_deteccion_premortem.md](26_marco_legal_deteccion_premortem.md)).
- **Un solo `SubscriptionTopic` estandarizado** ("detección-donante") reutilizable entre hospitales resuelve la limitación de "código a medida por institución" del estudio de Texas ([21_automatizacion_referral_ehr_estudio.md](21_automatizacion_referral_ehr_estudio.md)).

## Citas relevantes

> "The Subscription resource is used to establish proactive event notifications from a FHIR server to another system." (HL7 FHIR R5)

> "...the only supported channel type being rest-hook and supported payload type being id-only." (athenahealth Event Subscription Platform, conforme R5 Backport)

> LOINC **9269-2** — "Glasgow coma score total".

## Observaciones

- **A verificar:** qué versión de FHIR implementa HSI hoy (R4 vs R4B) y si tiene `Subscription`/`SubscriptionTopic` habilitado en producción o sólo lectura/escritura por API. Esto define si el piloto va por push o por polling.
- **A verificar:** si el HIS de un hospital piloto (p. ej. HIBA, [16_hospital_italiano_hiba_fhir.md](16_hospital_italiano_hiba_fhir.md)) ya expone Subscription.
- El Glasgow se registra como `Observation` **asistencial** (lo carga enfermería), no lo emite el monitor de signos vitales → ver el matiz crítico en [23_monitores_uti_captura_datos.md](23_monitores_uti_captura_datos.md).
