# Análisis — Adaptador de detección de potenciales donantes desde el HIS (alertas Glasgow ≤7, pre-mortem)

> Análisis crítico del vector "Alertas Glasgow ≤7 desde HIS hospitalarios" (WORKFLOW.md § 9, vector #1).
> Encuadre del equipo: **investigar a fondo un adaptador de sistemas de historias clínicas que detecte posibles donantes incluso antes de su fallecimiento, para avisar a la persona encargada de donantes (coordinador hospitalario) y que esté atenta a ese paciente.**
>
> Complementa a [01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md) (identificación de pacientes NN, otra etapa del pipeline).
>
> **Fecha:** 2026-05-31

---

## 0. TL;DR

| Pregunta | Veredicto |
|---|---|
| ¿Resuelve un problema real? | **Sí** — la subdetección de donantes es el cuello de botella #1 del sistema argentino (research.md §8). |
| ¿Es legal detectar antes de la muerte? | **Sí**, con cuidado de datos. La Ley 27.447 Art. 14 **obliga** a los establecimientos a detectar; el INCUCAI ya norma el criterio (PSG<7). La presunción de donación no aplica pre-mortem, así que la alerta debe quedarse **dentro del hospital** y minimizar datos ([sources/26](../sources/26_marco_legal_deteccion_premortem.md)). |
| ¿Hay evidencia de impacto? | **Sí, fuerte** — automatizar el referral por triggers (vent + UCI + Glasgow) dio **+92 % donantes efectivos** en hospitales piloto de Texas ([sources/21](../sources/21_automatizacion_referral_ehr_estudio.md)). |
| ¿Es viable técnicamente en ≤6 meses (deadline AFIDEER)? | **Sí para un MVP**: adaptador que recibe un evento FHIR/HL7 con Glasgow ≤7 y alerta al coordinador. La parte difícil no es el código, es **conseguir un HIS real para integrar**. |
| **Recomendación única** | **Construir un "DonorTrigger": un adaptador independiente del HIS que detecta Glasgow ≤7 en paciente de UTI ventilado y notifica al coordinador hospitalario como una worklist (no un pop-up al intensivista).** Empezar con la **Arquitectura B (polling/HL7-v2)** para el MVP de demo, diseñada para migrar a **Arquitectura A (FHIR Subscription)** en el piloto. Piloto sobre **HSI** (público, escalable) o **HIBA** (privado, maduro en FHIR). |

**Por qué este vector y no otro:** es el único donde el criterio clínico **ya está en la norma argentina** (PSG<7), el destinatario **ya existe** (coordinador hospitalario, Res. 1642/22) y hay **evidencia cuantitativa internacional** de que mover este punto casi duplica donantes. Riesgo regulatorio bajo, impacto potencial altísimo. Es complementario —no competidor— de SINTRA y del módulo de identificación RENAPER.

---

## 1. Encuadre del problema

### Qué resuelve
La **subdetección de potenciales donantes en UTI**: pacientes neurocríticos que evolucionan a muerte encefálica (o a WLST) y que **no son derivados a tiempo** al circuito de donación porque la detección depende de que un humano sobrecargado se acuerde de mirar y avisar.

La solución detecta el disparador clínico que el propio INCUCAI definió —**Glasgow ≤7 en paciente neurocrítico (PSG<7)**, [sources/19](../sources/19_incucai_deteccion_psg7_garantia_calidad.md)— directamente desde el sistema de historia clínica, **mientras el paciente todavía está vivo**, y le avisa al **coordinador hospitalario** para que lo evalúe y lo siga.

### Qué NO resuelve (límites explícitos)
- **No decide ni sugiere conducta clínica** sobre el paciente. Sólo señala "este caso cumple el criterio de detección".
- **No conversa con la familia** ni dispara el pedido de donación (eso es post-muerte y lo hace el equipo, respetando el **desacople** — [sources/20](../sources/20_triggers_clinicos_premortem_nhs_usa.md)). Nota legal: bajo la Ley Justina **la familia no autoriza la donación de un adulto** (donante presunto, Art. 33); se la informa y se verifica la voluntad, pero no es un consentimiento que condicione el proceso — ver [sources/04](../sources/04_ley_27447_justina.md) (Art. 33) y la etapa 2 en [06_disparo_logistica_muerte_encefalica.md](06_disparo_logistica_muerte_encefalica.md).
- **No identifica pacientes NN** — eso es el otro vector (RENAPER, [analisis/01](01_propuesta_deteccion_facial_renaper.md)).
- **No reemplaza al coordinador** — lo asiste; le arma la lista de pacientes a vigilar.
- **No escanea masivamente la UTI** ni hace inferencias con cámara/AI sobre pacientes (sería invasivo). Lee un dato que el personal ya registra.

### La idea en una frase
> Un paciente entra a UTI, enfermería registra Glasgow 6 → el HIS emite un evento → el adaptador lo detecta → el coordinador hospitalario recibe en su tablero "Cama 7, UTI: criterio de detección cumplido, revisar elegibilidad" — **horas o días antes** de que el caso hubiera llegado al circuito por la vía manual.

---

## 2. Capacidades / restricciones técnicas

### Lo que la tecnología SÍ puede hoy
- **Leer el Glasgow del HIS como dato estructurado** si está codificado como `Observation` LOINC **9269-2** (estándar FHIR que Argentina adoptó por Res. 680/2018) — [sources/22](../sources/22_fhir_subscription_codificacion_glasgow.md).
- **Recibir el evento por push** vía FHIR Subscription (`rest-hook`/webhook) en HIS que la soporten, o por **polling** / **HL7 v2 (`ORU^R01`)** en los que no.
- **Combinar criterios** objetivos del HIS: Glasgow ≤7 + UCI + ventilación mecánica (los tres son campos estructurados).
- **Enrutar la alerta a un canal propio del coordinador** (app/dashboard/push), deduplicada y accionable.

### Restricciones reales
- **El Glasgow NO sale del monitor de signos vitales** — es una valoración asistencial cargada por enfermería ([sources/23](../sources/23_monitores_uti_captura_datos.md)). ⇒ Hay que leer del HIS, no del monitor. La frescura del dato depende de la frecuencia de registro (típicamente por turno) → **la latencia la limita la práctica de carga, no la tecnología**.
- **Heterogeneidad de HIS:** muchos hospitales públicos no tienen HIS con FHIR; algunos guardan el Glasgow como **texto libre** en la evolución, no estructurado → ahí el filtro automático no funciona sin NLP.
- **SINTRA no participa** en esta capa (no tiene APIs) — pero **no hace falta**: la detección es intrahospitalaria, entre HIS y coordinador.
- **Alert fatigue:** si se diseña mal, el sistema se vuelve ruido y lo ignoran ([sources/25](../sources/25_alert_fatigue_cds_uti.md)).

---

## 3. Arquitectura propuesta

### Componente central: el adaptador "DonorTrigger"
Servicio independiente, desacoplado del HIS, con tres responsabilidades: **(1) ingestar** el dato clínico (por la vía que el HIS permita), **(2) evaluar** el criterio (Glasgow ≤7 + UCI + ventilado, umbral configurable) y deduplicar por paciente-episodio, **(3) notificar** al coordinador por su canal, de forma seudonimizada y auditada.

```
   ┌─────────────────────────── HOSPITAL ───────────────────────────┐
   │                                                                 │
   │  Enfermería UTI ──registra Glasgow=6──► HIS / HCE               │
   │                                          │                      │
   │                          (A) FHIR Subscription rest-hook        │
   │                          (B) polling GET /Observation           │
   │                          (C) listener HL7 v2 ORU^R01            │
   │                                          ▼                      │
   │                               ┌────────────────────┐            │
   │                               │  ADAPTADOR          │            │
   │                               │  "DonorTrigger"     │            │
   │                               │  evalúa + deduplica │            │
   │                               │  + anonimiza + log  │            │
   │                               └─────────┬──────────┘            │
   │                                         │ alerta (id-only)      │
   │                                         ▼                       │
   │                          ┌──────────────────────────┐           │
   │                          │ Coordinador hospitalario  │  ◄── worklist, NO pop-up
   │                          │ (app / dashboard / push)  │      al intensivista
   │                          └──────────────────────────┘           │
   └─────────────────────────────────────────────────────────────────┘
        (sólo si evoluciona a muerte encefálica → recién ahí, circuito CUCAI/SINTRA)
```

### Tres alternativas de ingesta (la decisión técnica central)

| Criterio | **A — FHIR Subscription (push)** | **B — Polling / API REST (pull)** | **C — IoT bypass del HIS** |
|---|---|---|---|
| Cómo obtiene el dato | El HIS emite webhook `rest-hook` cuando aparece `Observation` Glasgow ≤7 ([sources/22](../sources/22_fhir_subscription_codificacion_glasgow.md)) | El adaptador consulta `GET /Observation?code=9269-2` cada N min | Caja en la UTI escucha el monitor (HL7 v2) + posible cámara/sensor |
| Latencia | **Segundos** | Minutos (= intervalo de polling) | Tiempo real fisiológico |
| Carga sobre el HIS | Mínima | Media/alta | Nula (no toca el HIS) |
| ¿Lee el Glasgow? | **Sí** | **Sí** | **No** (el Glasgow no está en el monitor) → sólo deterioro fisiológico |
| Requisitos del HIS | FHIR R4B/R5 con Subscription habilitada | API FHIR/REST de lectura | Ninguno |
| Viable en HIS argentinos hoy | Pocos (a verificar HSI/HIBA) | **Mayoría** si exponen API | Cualquier UTI con monitor en red |
| Datos sensibles | Bajo (`id-only` + fetch) | Medio | Bajo |
| Esfuerzo de integración | Medio (config del HIS) | **Bajo** | Alto (hardware por cama) |
| Rol recomendado | **Objetivo del piloto** | **MVP / fallback universal** | **Complemento** (pre-alerta), o último recurso sin HIS digital |

**Decisión:** diseñar el adaptador con una **interfaz de ingesta intercambiable** (puerto/adapter pattern) para soportar A, B y C detrás del mismo motor de evaluación. **Empezar por B** (funciona contra casi cualquier HIS con API, ideal para demo AFIDEER) y **migrar a A** en el piloto con el HIS que lo permita. **C** queda como señal complementaria de deterioro o como única opción en hospitales sin HIS digital — explícitamente **no** sustituye la lectura del Glasgow.

### Modelo de datos (perfil FHIR de detección)
- `Observation` (LOINC 9269-2, Glasgow total; componentes 9268-4 motor, 9270-0 verbal, 11324-1 ocular).
- `Encounter` (clase = UTI, para confirmar internación en terapia intensiva).
- `Device`/`Observation` de ventilación (confirma ventilación mecánica).
- `Communication`/`Task` (la alerta al coordinador, auditable).
- No existe IG FHIR de trasplante argentino → **oportunidad de definir un `SubscriptionTopic` "detección-donante" estandarizado y reutilizable** (resuelve la limitación "código a medida por hospital" del estudio de Texas, [sources/21](../sources/21_automatizacion_referral_ehr_estudio.md)).

---

## 4. Análisis de seguridad

| Amenaza | Prob. | Impacto | Mitigación |
|---|---|---|---|
| Exfiltración de datos de salud (sensibles, Ley 25.326) | Media | Alto | Payload `id-only` en la alerta; datos completos sólo vía fetch TLS mutuo; cifrado en reposo; retención mínima. |
| Webhook/endpoint del adaptador expuesto | Media | Alto | mTLS, firma HMAC del webhook, allowlist de IP del HIS, sin endpoint público. |
| Acceso indebido del operador a la worklist | Media | Medio | RBAC, secreto profesional, **auditoría inmutable** de cada acceso (alineado a la trazabilidad de la Ley 27.447, Art. 57 inc. 1). |
| Alerta enviada a destinatario equivocado | Baja | Alto | Targeting estricto al coordinador del efector; seudonimización; sin reenvío externo automático. |
| Manipulación del trigger (suprimir/forzar alertas) | Baja | Alto | Logs append-only, control de cambios de umbral, alertas de integridad. |
| Caída del adaptador → donantes no detectados | Media | Alto | Servicio redundante; modo polling de respaldo; healthchecks; el proceso manual **sigue vigente** como fallback (no se elimina). |
| Dependencia de un proveedor de HIS | Media | Medio | Adaptador agnóstico (puerto de ingesta intercambiable A/B/C). |

**Principio rector:** el sistema es **aditivo**. Nunca debe **bloquear** ni reemplazar el proceso clínico ni el manual de detección; sólo agrega una vía más para no perder casos.

---

## 5. Análisis legal

Marco completo en [sources/26](../sources/26_marco_legal_deteccion_premortem.md). Resumen accionable:

| Norma | Qué habilita / exige | Cómo cumplir |
|---|---|---|
| **Ley 27.447 Art. 14** | **Obliga** a establecimientos a tener servicios que permitan la correcta **detección** del donante. | La herramienta **ayuda a cumplir** un deber existente → fuerte argumento de legitimidad. |
| **Ley 27.447 Art. 39** | El deber de **iniciar el proceso** de donación es del médico **al certificar la muerte** (post-mortem). | La alerta pre-mortem queda como **detección/preparación**, no como inicio del proceso. |
| **Ley 26.529** | Confidencialidad de datos sensibles; paciente titular de la HC. | Acceso sólo por personal con rol y secreto profesional; auditoría. |
| **Ley 25.326** | Datos de salud = **sensibles**; tratamiento con base legal. | Minimización (`id-only`), seudonimización, finalidad sanitaria, **DPIA** + dictamen **AAIP**. |

**Punto delicado y su solución:** como el paciente **está vivo**, la presunción de donación (Ley 27.447) **todavía no aplica**. ⇒ La alerta pre-mortem debe **quedarse dentro del establecimiento** (destinatario = coordinador hospitalario, que es personal propio dentro de la relación asistencial) y **no** volcar datos identificables a un tercero externo (CUCAI/INCUCAI) hasta que haya base legal más fuerte (evolución a muerte encefálica). Recién ahí se conecta el circuito externo.

**Recomendación legal:** DPIA antes del piloto + nota de consulta a la AAIP + validación del articulado del Art. 14 con asesoría jurídica (citado en sentido, confirmar texto literal).

---

## 6. Contactos / canales para avanzar

| Actor | Para qué | Canal | Tiempo esperado |
|---|---|---|---|
| **Casa Justina** | Sponsor; abre puertas a INCUCAI y hospitales; valida clínicamente | https://www.casajustina.org | Inmediato (es el sponsor) |
| **Coordinador hospitalario** (1-2, de un hospital aliado) | Validar la worklist, definir umbral y canal, medir carga tolerable | Vía Casa Justina / UAP | Semanas |
| **INCUCAI — Subprograma de Garantía de Calidad** | Confirmar criterio PSG<7, instrumento de medición, métrica de detección | Nota formal vía Casa Justina | 1-3 meses |
| **Lamansys / Instituto PLADEMA (UNICEN)** | Integración con **HSI** (open source) — versión FHIR, Subscription, modelo del Glasgow | UNICEN (ámbito universitario, afín AFIDEER) | 1-2 meses |
| **HIBA — Innova Salud Digital** | Piloto FHIR en hospital maduro ([sources/16](../sources/16_hospital_italiano_hiba_fhir.md)) | Vía Casa Justina / contacto académico | 1-3 meses |
| **AAIP** | Dictamen sobre datos sensibles pre-mortem | https://www.argentina.gob.ar/aaip | 2-4 meses |
| **CUCAI Entre Ríos** | Piloto provincial (cercanía UAP) | cucai@entrerios.gov.ar (verificar) | 1-3 meses |

---

## 7. Plan de implementación

| Fase | Plazo | Entregable | Dependencia crítica |
|---|---|---|---|
| **F0 — Validación del problema** | Mes 0-1 | Entrevistas a 2-3 coordinadores: ¿cómo detectan hoy? ¿qué worklist usarían? | Acceso vía Casa Justina |
| **F1 — MVP de demo (AFIDEER)** | Mes 1-3 | Adaptador (Arq. B) que recibe `Observation` Glasgow ≤7 simulado, evalúa, deduplica y muestra worklist al coordinador. Servidor FHIR de prueba (HAPI) como HIS mock. | Ninguna externa (todo simulado) |
| **F2 — Perfil FHIR + Subscription** | Mes 3-5 | `SubscriptionTopic` "detección-donante"; migración a Arq. A; pruebas en Conectaton del Ministerio | Acceso a un sandbox FHIR (HSI/HIBA) |
| **F3 — Piloto en 1 hospital** | Mes 5-9 | Despliegue real en 1 UTI; medición de detección vs. baseline; DPIA + dictamen AAIP | Convenio + HIS real + legal |
| **F4 — Piloto 5 hospitales** | Mes 9-18 | Escala a 5 UTIs (idealmente vía HSI 1-a-muchos); reporte de impacto | Financiamiento + adopción |

**Camino crítico:** no es el software (un MVP se construye en semanas), es **conseguir un HIS real con UTI y permiso legal**. Por eso F1 se hace 100 % simulado para llegar a AFIDEER sin bloquearse, y F3 es donde está el riesgo real.

---

## 8. Costos / BOM

Tres tiers, alineados al formato de [03_sensor_huella_costo_bom.md](03_sensor_huella_costo_bom.md). Este vector es **software-first**: el costo dominante es desarrollo (horas), no hardware.

### Tier A — Prototipo / Demo AFIDEER

| Concepto | USD | Notas |
|---|---|---|
| Hosting (VPS / free tier AWS/Arsat) | $0-20/mes | Adaptador + servidor FHIR mock (HAPI) |
| Servidor FHIR de prueba | $0 | HAPI FHIR open source, dockerizado |
| Front del coordinador (web simple) | $0 | React/HTMX, hosting estático |
| Notificación push (demo) | $0 | FCM free tier / email |
| Desarrollo | $0 | Estudiantes UAP (horas) |
| **Total demo** | **~$0-60** | Todo software + simulación |

### Tier B — Piloto 1 hospital

| Concepto | USD (12 meses) | Notas |
|---|---|---|
| Hosting productivo (intra-hospital o nube nacional Arsat) | $600-1.200 | Con redundancia básica |
| Integración con HIS real (config FHIR/HL7) | $1.000-3.000 | Horas de integración + soporte del proveedor del HIS |
| Notificaciones (push/SMS coordinador) | $50-300 | Twilio/FCM, volumen bajo |
| DPIA + asesoría legal | $1.000-2.500 | Evaluación de impacto + dictamen |
| Hardware (sólo si Arq. C / bridge HL7) | $0-500 | Mini-PC/gateway si el hospital no expone API |
| Desarrollo / ajustes | pro bono - $5.000 | Estudiantes UAP + posible mentor pago |
| **Total piloto 1 hospital** | **~$2.650-12.500** | Domina legal + integración, no hardware |

### Tier C — Producción 5 hospitales

| Concepto | USD/año | Notas |
|---|---|---|
| Hosting multi-tenant | $2.000-4.000 | 1 instancia, varios efectores |
| Integraciones (5 HIS o **1 sola vía HSI**) | $2.000-8.000 | **Si todos usan HSI: 1 integración cubre los 5** ([sources/24](../sources/24_hsi_historia_salud_integrada.md)) |
| Notificaciones | $300-1.000 | |
| Mantenimiento + soporte | $3.000-6.000 | 1 dev part-time |
| Gobernanza de alertas (medición/ajuste) | incluido | Tablero de métricas |
| **Total producción 5 hospitales** | **~$7.300-19.000/año** | El gran ahorro está en integrar vía HSI (1-a-muchos) |

### Comparación de valor
- Un trasplante hepático perdido le cuesta al sistema **>USD 50.000** (research.md / [03](03_sensor_huella_costo_bom.md)).
- El estudio de Texas mostró **+0,7 donantes/mes por grupo de 3 hospitales** automatizando ([sources/21](../sources/21_automatizacion_referral_ehr_estudio.md)). Cada donante ≈ varios trasplantes.
- **Si el piloto evita perder 1 donante, se paga muchas veces.** Es el argumento económico más fuerte del repo.

---

## 9. Riesgos de proyecto (no técnicos)

| Riesgo | Prob. | Impacto | Mitigación |
|---|---|---|---|
| No conseguir un HIS real para el piloto | **Alta** | Alto | F1 100 % simulado para no bloquear AFIDEER; gestionar HSI/HIBA en paralelo vía Casa Justina desde el mes 0. |
| Glasgow guardado como texto libre (no estructurado) en el HIS objetivo | Media | Alto | Verificar en F0; si es texto libre, evaluar NLP o priorizar otro efector con dato estructurado. |
| Rechazo médico ("otra alerta más") | Media | Medio | No alertar al intensivista; worklist para el coordinador; CDS pasivo ([sources/25](../sources/25_alert_fatigue_cds_uti.md)). |
| Demora legal (AAIP/DPIA) | Media | Medio | Empezar la consulta temprano; piloto sobre datos seudonimizados. |
| Percepción de "vigilancia masiva de UTI" | Media | Alto | Comunicar que lee un dato ya registrado, no escanea; respeta el desacople; aditivo, no decide. |
| Cambio de gestión INCUCAI / política | Baja | Medio | Apoyarse en que el criterio ya está normado (no depende de una gestión). |

---

## 10. Métricas de éxito

| Métrica | Baseline (a medir en F0) | Meta piloto | Cómo se mide |
|---|---|---|---|
| **Tasa de detección de potenciales donantes** (detectados / muertes encefálicas) | el actual del Subprograma de Calidad | +X % (definir con coordinador) | Comparar vs. histórico del hospital |
| **Tiempo desde PSG<7 hasta aviso al coordinador** | horas/días (manual) | minutos | Timestamp evento HIS → notificación |
| **Cobertura del criterio** (casos PSG<7 que generaron alerta / casos reales) | — | >95 % | Auditoría contra HC |
| **Tasa de acción del coordinador** (alertas atendidas / enviadas) | — | >80 % | Log de la worklist |
| **Falsos positivos / sobrecarga** | — | <X/semana tolerable | Feedback del coordinador |
| **Donantes efectivos adicionales** | el actual | señal positiva (no extrapolar lineal) | Registro de procuración |

---

## 11. Ficheros relacionados

**Fuentes que sustentan este análisis:**
- [../sources/19_incucai_deteccion_psg7_garantia_calidad.md](../sources/19_incucai_deteccion_psg7_garantia_calidad.md) — criterio PSG<7 normado en Argentina
- [../sources/20_triggers_clinicos_premortem_nhs_usa.md](../sources/20_triggers_clinicos_premortem_nhs_usa.md) — triggers pre-mortem (NHS/USA) y desacople
- [../sources/21_automatizacion_referral_ehr_estudio.md](../sources/21_automatizacion_referral_ehr_estudio.md) — evidencia +92 % donantes
- [../sources/22_fhir_subscription_codificacion_glasgow.md](../sources/22_fhir_subscription_codificacion_glasgow.md) — mecánica del adaptador (FHIR Subscription, LOINC 9269-2)
- [../sources/23_monitores_uti_captura_datos.md](../sources/23_monitores_uti_captura_datos.md) — por qué el Glasgow se lee del HIS, no del monitor
- [../sources/24_hsi_historia_salud_integrada.md](../sources/24_hsi_historia_salud_integrada.md) — HSI como vía de escala 1-a-muchos
- [../sources/25_alert_fatigue_cds_uti.md](../sources/25_alert_fatigue_cds_uti.md) — diseño anti alert fatigue
- [../sources/26_marco_legal_deteccion_premortem.md](../sources/26_marco_legal_deteccion_premortem.md) — marco legal pre-mortem
- [../sources/08_interoperabilidad_fhir_argentina.md](../sources/08_interoperabilidad_fhir_argentina.md) — FHIR nacional (Res. 680/2018)
- [../sources/14_cuellos_botella_operacionales.md](../sources/14_cuellos_botella_operacionales.md) — cuello #1 subdetección
- [../sources/16_hospital_italiano_hiba_fhir.md](../sources/16_hospital_italiano_hiba_fhir.md) — HIBA como piloto privado

**Análisis relacionados:**
- [01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md) — etapa siguiente (identificar al donante detectado, si es NN)
- [04_modelo_dinamico_viabilidad_cardiaca.md](04_modelo_dinamico_viabilidad_cardiaca.md) — etapa posterior (viabilidad del órgano una vez procurado)

**Síntesis:** research.md §8 (cuello #1) y §9 (oportunidad A8).
