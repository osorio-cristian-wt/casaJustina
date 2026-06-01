# Análisis crítico — Detección facial RENAPER/Mi Argentina en hospitales para acelerar el pipeline de donación

> **Desafío AFIDEER 2026 — Casa Justina**
>
> Propuesta evaluada: usar el Sistema de Identidad Digital (SID) del RENAPER y/o Mi Argentina para reconocer pacientes en hospitales y acelerar la detección de potenciales donantes de órganos.
>
> **Fecha:** 2026-05-14

---V

## 0. TL;DR — veredicto ejecutivo

La propuesta **no resuelve "detección de potenciales donantes" en sentido estricto** (ese gap es Glasgow ≤7 + comunicación familiar), pero **sí resuelve un sub-problema real y valioso**: identificar rápido a pacientes **NN** (sin documento) y acortar el ciclo *identidad → registro de voluntad → contacto familiar*.

**Veredicto:** **viable, valiosa y diferenciada** si se acota correctamente. Requiere:
- Combinar **biometría facial + huella RENAPER** (no solo facial — el paciente está inconsciente, no hay liveness).
- Convenio formal RENAPER + INCUCAI + hospital piloto.
- Umbrales de confianza ≥ 99,5 % + segundo factor humano obligatorio.
- Mecanismo de auditoría inmutable (trazabilidad, Art. 57 inc. 1 Ley 27.447).

**Aporte único del proyecto:** integrar dos sistemas estatales (RENAPER + INCUCAI) que **hoy no se hablan**, lo cual reduce horas/días del workflow para pacientes NN.

---

## 1. Encuadre del problema

### Qué dice el gap actual

| Brecha medida | Magnitud | Lo resuelve la propuesta? |
|---|---|---|
| Subdetección Glasgow ≤7 en UTI | 3× peor que España | **No directamente** |
| Negativa familiar (40 %) | 3× peor que España | **No** |
| Identificación de pacientes **NN** | Días/semanas | **Sí** ✅ |
| Contacto con familia del NN | Demorado | **Sí, parcial** ✅ |
| Consulta del registro de voluntad cuando hay duda de identidad | Mayoritariamente manual | **Sí** ✅ |
| Heterogeneidad de procesos entre 24 CUCAI | Total | No |

La propuesta apunta entonces a **un subconjunto** del problema: el ciclo identidad → voluntad → familia.

### Cuántos casos por año puede impactar (estimación)

- Argentina: 1.972 procesos de donación (2024) sobre ~7.000–8.000 potenciales detectados.
- Trauma vial mortal: ~6.000 muertes/año. Muchas víctimas llegan sin documentación.
- **Estimación conservadora:** 200–500 operativos/año donde la identidad del donante es un cuello de botella → 10–25 % de operativos podrían beneficiarse.
- Si reduce CIT en 1–2 horas en esos casos → impacto directo en viabilidad de órganos.

### Por qué no es "scan masivo de UTI"

No hace falta reconocimiento facial para saber si un paciente con DNI puede ser donante: SINTRA ya lo permite con DNI. **El valor agregado es donde no hay DNI o donde la identidad está en duda.**

---

## 2. Capacidades reales del SID RENAPER

(Detalle técnico completo en [sources/18_renaper_sid_capabilities.md](../sources/18_renaper_sid_capabilities.md))

| Servicio SID | Integración | Liveness | ¿Sirve con paciente inconsciente? |
|---|---|---|---|
| Validación DNI (datos) | REST | N/A | Solo si hay DNI |
| **Validación facial** | REST | **No incluido en API REST** | **Sí** (con cámara fija + flujo no-gesto) |
| **Validación huella** | SOAP | No incluido | **Sí** (más confiable en este escenario) |
| Flujo SID completo (selfie + gestos) | SDK móvil | Sí | **No** (paciente no coopera) |

### Implicancia técnica clave

**El flujo "Mi Argentina" estándar (selfie + gestos) es inutilizable con un paciente inconsciente.** Por lo tanto:

- **Facial sí**, pero usando la API REST de Validación Facial (sin liveness por gestos), con cámara hospitalaria estándar y **mitigaciones alternativas anti-spoofing**: cámara fija calibrada, fotografía en condiciones controladas, validación cruzada con huella.
- **Huella RENAPER** (SOAP) es **probablemente el factor más confiable** en este escenario: el paciente está físicamente disponible, sensor capacitivo barato.
- **Multi-modal obligatorio**: facial + huella + (DNI si lo hay) → reduce falsos positivos a tasa aceptable.

### Datos que devuelve el SID
- Score / porcentaje de coincidencia.
- Datos públicos: nombre, apellido, DNI, fecha de nacimiento, género, domicilio.
- Validez documento.
- (No devuelve: voluntad de donación. Eso vive en SINTRA / INCUCAI.)

---

## 3. Arquitectura propuesta (alto nivel)

```
   ┌─────────────────────────────────────────────────────────┐
   │              HOSPITAL — UTI / Guardia                   │
   │  Cámara fija + sensor huella + tablet/PC                │
   │  Operador: enfermero/coordinador hospitalario           │
   └────────────────────┬────────────────────────────────────┘
                        │ HTTPS
                        ▼
   ┌─────────────────────────────────────────────────────────┐
   │   Gateway "DonorID" (nuevo — este proyecto)             │
   │   - Recibe captura facial + huella                      │
   │   - Orquesta llamadas a SID                             │
   │   - Cruza identidad obtenida con SINTRA (consultar      │
   │     manifestación de voluntad + lista de espera)        │
   │   - Audit log append-only (inmutable)                   │
   │   - Devuelve: identidad + voluntad + datos contacto     │
   └────────┬──────────────────────┬─────────────────────────┘
            │ REST/SOAP            │ HTTPS API (a definir
            ▼                      │ con INCUCAI)
   ┌─────────────────┐     ┌───────▼─────────────────────────┐
   │ RENAPER SID     │     │ SINTRA / INCUCAI                │
   │ (facial+huella) │     │ (registro voluntad, lista)      │
   └─────────────────┘     └─────────────────────────────────┘
                                    │
                                    ▼
   ┌─────────────────────────────────────────────────────────┐
   │ Notificación a coordinador hospitalario + CUCAI prov.   │
   │ Push + dashboard timeline operativo                     │
   └─────────────────────────────────────────────────────────┘
```

### Componentes nuevos a construir (alcance del proyecto)
1. **Cliente hospitalario** — webapp/tablet con captura facial + lector de huella.
2. **Gateway DonorID** — backend que orquesta SID + SINTRA + auditoría.
3. **Adaptador SINTRA** — el bloqueo regulatorio mayor; estado actual: **SINTRA no tiene API pública**, hay que negociar acceso institucional con INCUCAI.
4. **Audit log append-only** — para la trazabilidad de la Ley 27.447 (Art. 57 inc. 1).
5. **Dashboard de notificación** — push al coordinador hospitalario.

### Componentes que ya existen
- RENAPER SID con API REST + SOAP.
- SINTRA (acceso vía web, NO vía API documentada → gap a resolver).
- HIS hospitalarios (variados, posible integración FHIR posterior).

---

## 4. Análisis de seguridad

### Amenazas y mitigaciones

| Amenaza | Probabilidad | Impacto | Mitigación |
|---|---|---|---|
| **Falsa identificación positiva** (identifico al paciente como persona equivocada) | Media en condiciones clínicas (edema, intubación, trauma facial) | Alto: violación de confidencialidad médica + posible inicio incorrecto del workflow | Umbral score ≥ 99,5 % + segundo factor (huella) + revisión humana obligatoria |
| **Falsa identificación negativa** (no logro identificar a un paciente que SÍ está en RENAPER) | Alta en condiciones clínicas | Medio: retraso, pero no daño | Fallback a flujo tradicional (identificación judicial); reintentos con limpieza facial |
| **Spoofing/foto de foto** sin liveness | Baja en hospital (operador presente) | Bajo | Cámara fija calibrada + presencia de operador + dual factor |
| **Filtración de datos biométricos** | Baja (RENAPER no expone templates) | Crítico legal | No almacenar templates localmente; sólo IDs de transacción |
| **Acceso indebido** del gateway al SID | Media | Alto | API Key + IP whitelist + auth por operador con MFA |
| **Manipulación del audit log** | Baja | Crítico (chain of custody legal) | Append-only ledger (blockchain o WORM storage) |
| **Inferencia de datos sensibles** vía SID por curiosidad | Media | Alto (datos de salud) | Logging completo + auditoría por organismo + sanciones internas |
| **Adversario interno** (operador malicioso) | Baja | Alto | Auth fuerte + segregación de roles + alertas en SIEM |

### Tasas FAR/FRR

RENAPER **no publica** tasas False Acceptance Rate (FAR) y False Rejection Rate (FRR). Para un caso de uso de alta sensibilidad como donación, **esto debe negociarse antes del piloto**:
- Pedir el SLA y métricas de exactitud al RENAPER.
- Validar con muestra clínica real (con consentimiento) en hospital piloto.
- Establecer umbral mínimo aceptable para que el sistema emita identificación positiva.

### Recomendación de seguridad
- **Multi-factor biométrico obligatorio** (facial + huella).
- **Umbral conservador** (>99,5 %).
- **Match positivo NO inicia acciones automáticas** — solo informa al coordinador hospitalario, que debe confirmar humanamente.
- **Audit log inmutable** con timestamping del RENAPER.
- **Pen-test + revisión de DGPR/ENACOM** antes de productivo.

---

## 5. Análisis legal

### Marco normativo aplicable

| Norma | Implicancia |
|---|---|
| **Ley 25.326** — Protección de Datos Personales | Cesión solo por interés legítimo + consentimiento previo (con excepciones de salud pública). |
| **Art. 11 Ley 25.326** | Finalidad directamente relacionada — la finalidad sanitaria de salvar vidas vía donación encuadra. |
| **Ley 26.529** — Derechos del Paciente | Identificación inequívoca del paciente como deber del prestador → la propuesta refuerza este deber. |
| **Ley 27.447** "Ley Justina" | Donante presunto (Art. 33) + trazabilidad (Art. 57 inc. 1) + confidencialidad de datos amparada por las Leyes 26.529 y 25.326. |
| **Ley 27.275** — Acceso a Información Pública | El algoritmo de identificación debe ser auditable. |
| **Ley 27.706** — Salud Digital | Habilita historia clínica electrónica interoperable. |
| **Convenio Único SID** | Documento que firma el organismo consumidor con RENAPER. Acceso vía TAD. |

### Riesgo legal principal: **mala identificación**

**Escenario crítico:** SID identifica al paciente como persona X, pero en realidad es persona Y. Persona X registró oposición a la donación; persona Y es donante presunto.

#### ¿Qué tan grave es esto en la práctica?

La identificación facial **NO autoriza la ablación**. Entre la identificación y la ablación hay múltiples gates:
1. Certificación de muerte encefálica (Res. 716/19) — requiere protocolo clínico independiente.
2. Verificación del DNI (cuando aparece) — segunda verificación.
3. Búsqueda del registro de voluntad por DNI.
4. Comunicación familiar — la familia confirma identidad de su ser querido.
5. Decisión judicial (si causa violenta/no esclarecida).
6. Aceptación del operativo por parte del equipo procurador.

→ Una mala identificación facial **es detectable y reversible** antes de la ablación.

#### Riesgos colaterales más realistas
- **Violación de confidencialidad** — el sistema muestra datos clínicos de persona X a un médico que está tratando a persona Y. Esto es problema serio y exige logging completo + minimización de datos retornados.
- **Stress familiar** — contactar a la familia equivocada por error de matching.
- **Responsabilidad civil del hospital** y del organismo consumidor — el daño moral derivado de identificación errónea es accionable.

#### Mitigaciones legales obligatorias

- **Consentimiento por reglamentación**: la Ley 25.326 exime del consentimiento individual cuando hay interés vital sanitario (Art. 5 inc. 2). Hay que documentar este encuadre.
- **DPIA** (Data Protection Impact Assessment) formal antes del piloto.
- **Convenio tripartito** Hospital ↔ INCUCAI ↔ RENAPER con responsabilidades de cada parte definidas.
- **Protocolo escrito** con reglas de actuación cuando hay match positivo, falso positivo, falso negativo.
- **Auditoría externa anual** del sistema.
- **Mecanismo de reclamo** del ciudadano (Art. 26 Ley 25.326).

### Dictamen necesario

Antes del piloto: solicitar **dictamen de la AAIP** (Agencia de Acceso a la Información Pública), órgano de aplicación de la Ley 25.326. Probabilidad alta de aprobación si el caso de uso está bien acotado.

---

## 6. Cómo contactar a los actores gubernamentales

### Lista priorizada de contactos

| Organismo | Para qué | Canal recomendado | Tiempo de respuesta esperado |
|---|---|---|---|
| **AFIDEER (Consorcio)** | Padrinazgo + intros con Casa Justina | Sus mentores (UNER/UADER/UAP/UCA/UTN) | Días — son los sponsors del desafío |
| **Casa Justina** | Validación del caso de uso + acceso a INCUCAI | Ezequiel Lo Cane / casa@justina.org.ar (verificar) | Semanas — recibe muchas propuestas |
| **INCUCAI — Dirección de Sistemas** | API/integración SINTRA | Mesa de ayuda institucional + nota formal | Semanas/meses |
| **INCUCAI — Dirección Médica** | Validación clínica del caso de uso | Vía Casa Justina o nota formal | Semanas |
| **RENAPER — Convenios SID** | Acceso al servicio SID | conveniosid@renaper.gob.ar + TAD | Semanas |
| **Ministerio de Salud — DNSIS** | Cumplimiento estándares FHIR | openrsd.msal.gov.ar + Conectaton | Días en eventos |
| **AAIP** | Dictamen sobre datos personales | aaip.gob.ar | Meses |
| **Hospital piloto** | Validación operativa | Vía Casa Justina (Argerich, El Cruce, HIBA, Garrahan) | Semanas |
| **CUCAI Entre Ríos** | Piloto provincial (cercano UAP) | cucai@entrerios.gov.ar | Semanas |
| **UAP — Facultad Cs. de la Salud + UAP Ingeniería** | Apoyo académico interdisciplinario | Tu propio campus | Días |

### Orden recomendado de aproximación

1. **AFIDEER** — son tu primer canal. Confirmá si hay intros formales pre-armadas con Casa Justina.
2. **Casa Justina** — necesitás su patrocinio formal antes de tocar puertas de INCUCAI/RENAPER (les confiere institucionalidad a la propuesta).
3. **INCUCAI — Dirección de Sistemas** — porque sin integración con SINTRA, el resto no aporta valor.
4. **RENAPER — Convenios SID** — paralelo al paso 3.
5. **Hospital piloto** — preferentemente uno donde Casa Justina ya tenga relación: HIBA (CABA), Hospital El Cruce (Florencio Varela), o uno provincial en Entre Ríos (Hospital San Roque, Hospital San Martín de Paraná).
6. **AAIP** — dictamen formal antes del piloto productivo.

### Cómo presentar la propuesta

- **No la presentes como "reemplazar SINTRA"** — política suicidio. Presentala como "ENRIQUECER SINTRA con identidad RENAPER".
- **No la presentes como "scan masivo"** — alarmas legales. Presentala como "asistencia para pacientes NN".
- **Cuantificá el impacto** — 200-500 operativos/año potencialmente beneficiados.
- **Mostrá el modelo Casa Justina+H+Trace** como precedente exitoso de innovación pública-privada en este espacio.

---

## 7. Plan de implementación (roadmap propuesto)

### Fase 0 — Validación (mes 1-2)
- Entrevistas con coordinadores hospitalarios (3-5) — confirmar pain point real.
- Entrevistas con coordinadores CUCAI provincial (Entre Ríos + 1 más).
- Validación legal preliminar (consulta no vinculante con AAIP).
- Documento de alcance funcional firmado con Casa Justina.

### Fase 1 — Prototipo (mes 3-5)
- Mock RENAPER SID (sin convenio aún) usando dataset sintético.
- Mock SINTRA (datos públicos + sintéticos).
- Webapp clínica + flujo operador.
- Audit log append-only.
- **Demo en Desafío AFIDEER**.

### Fase 2 — Convenios y piloto cerrado (mes 6-9)
- Firma convenio SID con RENAPER.
- Convenio bilateral INCUCAI (sandbox SINTRA si hay disposición).
- Selección de hospital piloto.
- DPIA formal.
- Pen-test.

### Fase 3 — Piloto productivo (mes 10-15)
- Despliegue en hospital piloto.
- 3-6 meses de operación con seguimiento.
- Métricas: tasa de identificación, falsos positivos, tiempo de identificación, impacto en CIT.
- Reporte para INCUCAI + AAIP.

### Fase 4 — Escala (post-piloto)
- Extensión a 5 hospitales adicionales.
- Eventualmente extensión a CUCAI provinciales.
- Integración con cadena de tracking IoT (H+Trace + contenedores).

---

## 8. Diferenciación frente a alternativas

| Alternativa | Pros | Contras | ¿Por qué nuestra propuesta es mejor? |
|---|---|---|---|
| **Status quo** (identificación manual) | Funciona | Demora horas/días para NN | Reduce tiempo a minutos |
| **Solo wristband + DNI** | Simple | No sirve para NN | Identificación funciona aunque no haya DNI |
| **Búsqueda manual en RENAPER por personal del hospital** | Posible hoy | Lento, sin trazabilidad, no escalable | Sistema automatizado + auditado + integrado a SINTRA |
| **Solo facial sin huella** | Más simple | Falsos positivos en condiciones clínicas | Multi-modal reduce error |
| **Plataforma genérica (sin caso de uso de donación)** | Reutilizable | No habla con SINTRA | Específica → integra dos sistemas estatales que hoy no se hablan |

### Valor único diferenciado
**Es la primera integración SID-RENAPER × SINTRA-INCUCAI documentada en Argentina.** Esta integración es difícil política y técnicamente, y por eso es defendible como ventaja competitiva del proyecto.

---

## 9. Riesgos de proyecto (no técnicos)

| Riesgo | Probabilidad | Impacto | Mitigación |
|---|---|---|---|
| INCUCAI no cede acceso a SINTRA | **Alta** | Crítico (sin esto, vale poco) | Plan B: piloto sólo identidad RENAPER + push a coordinador (sin SINTRA), demostrar valor → presionar después por SINTRA |
| RENAPER demora convenio | Media | Alto | Avanzar con mocks; usar Casa Justina para acelerar TAD |
| Hospital piloto se baja | Media | Alto | Tener 2 hospitales en stand-by |
| AAIP exige rediseño | Media | Medio | Hacer DPIA temprano + dictamen no vinculante |
| Cambio de gobierno → desinterés político | Media | Alto | Sponsor ONG (Casa Justina) sobrevive ciclos políticos |
| Sobre-prometer en el desafío AFIDEER | Baja | Medio | Acotar alcance del MVP claramente |
| Competencia (otro equipo presenta similar) | Baja | Bajo | La integración SINTRA es la barrera de entrada — defendible |

---

## 10. Métricas de éxito

### Métricas técnicas (MVP)
- Latencia identificación: < 10 segundos (P95).
- Tasa de identificación correcta: > 95 % en pacientes con datos en RENAPER.
- Falsos positivos: < 0,5 %.
- Disponibilidad: 99,5 %.

### Métricas operativas (piloto 6 meses en hospital)
- Casos NN identificados vía sistema: > 10/mes.
- Tiempo medio identidad → consulta voluntad: < 15 minutos (vs horas/días hoy).
- Tiempo medio identidad → contacto familiar: < 1 hora (vs ~24 h hoy).
- Operativos exitosos en pacientes NN: incremento medible.

### Métricas de impacto (largo plazo)
- Reducción del CIT promedio en operativos donde la identificación fue cuello de botella.
- Operativos adicionales/año habilitados por identificación rápida.
- Adopción por otros hospitales.

---

## 11. Stack tecnológico sugerido

### Frontend (terminal hospitalario)
- React/Next.js o Vue 3 — webapp responsive.
- WebRTC para captura de cámara.
- WebUSB / WebHID para sensor de huella.
- Tablet/PC con Windows o ChromeOS — coexistencia con HIS.

### Backend
- Node.js (Fastify) o Python (FastAPI) — gateway.
- PostgreSQL para metadata operacional.
- Redis para caché de sesión.
- WORM storage (S3 Object Lock) o ledger append-only para audit.

### Integración
- HTTPS TLS 1.2+ (requisito SID).
- IPsec VPN al RENAPER.
- Cliente SOAP (huella RENAPER).
- API Gateway con WAF.
- OAuth2 + MFA para operadores.

### Observabilidad
- OpenTelemetry → Grafana / Datadog.
- SIEM (Wazuh open source).
- Alertas SLA.

### Despliegue
- Kubernetes en cloud nacional (Arsat) o hybrid on-prem hospitalario.
- CI/CD GitHub Actions.

---

## 12. Próximos pasos concretos para el equipo UAP

### Esta semana
1. Confirmar con AFIDEER las reglas y entregables del desafío.
2. Solicitar reunión con Casa Justina (carta breve + esta propuesta resumida en 1 página).
3. Buscar contactos en INCUCAI (Dirección de Sistemas) — vía AFIDEER o UAP Facultad de Ciencias de la Salud.

### Próximas 2 semanas
4. Entrevistar 2-3 coordinadores hospitalarios (HIBA, hospital provincial Entre Ríos).
5. Solicitar info formal a RENAPER vía `conveniosid@renaper.gob.ar` (ya con apoyo Casa Justina).
6. Prototipo "hola mundo" del flujo: cámara → score simulado → consulta SINTRA mockeada.

### Próximo mes
7. Demo en presentación intermedia AFIDEER.
8. Documento legal preliminar.
9. Pitch refinado de cara a la final.

---

## 13. Argumento moral / pitch para el jurado

> Cuando una persona muere sin identificación, no solo pierde su historia médica: pierde la posibilidad de cumplir su última voluntad — donar. En Argentina, cientos de pacientes anónimos llegan cada año a UTI tras accidentes. Identificarlos rápido no es un lujo técnico: es respetar lo que la persona ya decidió. Conectar RENAPER con INCUCAI permite que un paciente NN deje de ser anónimo en minutos, en lugar de días. Cada hora ganada es un órgano que puede salvar una vida. Justina murió porque no llegó a tiempo. Esto evita que vuelva a pasar.

---

## 14. Apéndices

### A. URLs útiles
- RENAPER SID: https://www.argentina.gob.ar/interior/renaper/sid-sistema-de-identidad-digital
- SID modalidades: https://www.argentina.gob.ar/sid/modalidades-y-productos
- Convenios SID: conveniosid@renaper.gob.ar
- INCUCAI sistemas: https://www.argentina.gob.ar/salud/incucai
- AAIP: https://www.argentina.gob.ar/aaip
- TAD: https://tramitesadistancia.gob.ar
- AFIDEER: paralelo32.com.ar / aimdigital.com.ar
- Casa Justina: https://www.casajustina.org

### B. Referencias internas
- [research.md — visión general del ecosistema](../research.md)
- [sources/02_sintra_modulos_arquitectura.md — SINTRA](../sources/02_sintra_modulos_arquitectura.md)
- [sources/04_ley_27447_justina.md — marco legal](../sources/04_ley_27447_justina.md)
- [sources/12_casa_justina_drones_htrace.md — precedente Casa Justina](../sources/12_casa_justina_drones_htrace.md)
- [sources/14_cuellos_botella_operacionales.md — gaps del sistema](../sources/14_cuellos_botella_operacionales.md)
- [sources/18_renaper_sid_capabilities.md — capacidades RENAPER](../sources/18_renaper_sid_capabilities.md)
