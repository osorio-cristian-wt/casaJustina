# Casa Justina — Detección y Notificación de Potenciales Donantes

> Repositorio de investigación + análisis para el **Desafío de Innovación Entre Ríos 2026** (AFIDEER), con sponsor **Casa Justina**.
>
> Equipo: estudiantes UAP (Universidad Adventista del Plata, Entre Ríos).

---

## ¿Qué es este proyecto?

El **sistema argentino de trasplantes pierde oportunidades de donación** porque la detección y notificación de potenciales donantes en terapias intensivas **no se realiza de forma sistemática ni integrada**. La Ley Justina (27.447) lo exige, pero la realidad operativa lo incumple.

Este repositorio contiene:

1. **Investigación profunda** sobre el ecosistema argentino de trasplantes (INCUCAI, SINTRA, marco legal, logística, interoperabilidad, comparación internacional).
2. **Análisis críticos** de soluciones específicas (la primera explorada: detección facial con RENAPER/SID).
3. **Metodología replicable** para continuar la investigación sin perder coherencia (ver [WORKFLOW.md](WORKFLOW.md)).

El objetivo final es **diseñar e implementar una solución técnica + operativa** que pueda presentarse en el Desafío AFIDEER 2026 (final en noviembre) y, idealmente, escalarse luego con Casa Justina como sponsor institucional.

---

## El desafío (enunciado canónico)

> **¿Cómo podríamos mejorar la identificación y notificación de potenciales donantes en terapias intensivas, de manera que el sistema de trasplantes reciba esta información de forma oportuna y confiable?**

Versión completa, restricciones y actores: [sources/00_desafio_casa_justina_enunciado.md](sources/00_desafio_casa_justina_enunciado.md).

### Dimensiones del problema (espacio de soluciones)

```
                  Detección y notificación de donantes
                                  │
        ┌─────────────────────────┼──────────────────────────┐
        │                         │                          │
   1. DETECTAR             2. IDENTIFICAR              3. NOTIFICAR
   (Glasgow ≤7,            (NN, validación             (CUCAI, INCUCAI,
    HIS hooks)              biométrica)                 coordinador)
                                                              │
                                                       ┌──────┴────────┐
                                                       │               │
                                                4. CAPACITAR     5. CUMPLIR
                                                (Ley Justina,    (auditar
                                                 microlearning)   protocolo)
```

El equipo puede atacar **una** dimensión a fondo o **varias** integradas. Estado actual del análisis en [sources/00_desafio_casa_justina_enunciado.md](sources/00_desafio_casa_justina_enunciado.md#categorías-de-solución-compatibles-con-el-enunciado).

---

## Estado actual del repositorio

| Bloque | Estado | Archivos |
|---|---|---|
| 📚 Investigación del ecosistema | **Completa (v1)** | `research.md` + 18 fuentes en `sources/` |
| 📘 Glosario de siglas | Completo | `GLOSARIO.md` |
| 🔬 Análisis: RENAPER facial | **Completo (3 docs)** | `analisis/01-03` |
| 🔬 Análisis: alertas Glasgow ≤7 desde HIS | Pendiente | — |
| 🔬 Análisis: notificación realtime CUCAI/INCUCAI | Pendiente | — |
| 🔬 Análisis: capacitación / microlearning | Pendiente | — |
| 🔬 Análisis: dashboard cumplimiento institucional | Pendiente | — |
| 📐 Arquitectura técnica de la solución | Esquemática (en `research.md` § 10) | — |
| 🎤 Pitch deck para AFIDEER | Pendiente | — |
| 📝 Notas formales a INCUCAI / RENAPER / Casa Justina | Pendiente | — |
| 🧑‍💻 Prototipo software | Pendiente | — |

---

## Estructura del repositorio

```
casaJustina/
├── README.md                     ← este archivo (front door)
├── WORKFLOW.md                   ← cómo continuar la investigación
├── GLOSARIO.md                   ← siglas y términos técnicos
├── research.md                   ← síntesis maestra del ecosistema
│
├── sources/                      ← fuentes documentadas (1 archivo por fuente)
│   ├── 00_desafio_casa_justina_enunciado.md         ← enunciado canónico
│   ├── 01_incucai_argentina_gob.md
│   ├── 02_sintra_modulos_arquitectura.md
│   ├── 03_pasos_operativos_trasplante.md
│   ├── 04_ley_27447_justina.md
│   ├── 05_tiempos_isquemia.md
│   ├── 06_aerolineas_convenio.md
│   ├── 07_matching_listas_espera.md
│   ├── 08_interoperabilidad_fhir_argentina.md
│   ├── 09_comparacion_internacional_unos_eurotransplant_nhs.md
│   ├── 10_donacion_asistolia_resolucion_327_2023.md
│   ├── 11_estadisticas_argentina_2024_2025.md
│   ├── 12_casa_justina_drones_htrace.md
│   ├── 13_modelo_espanol_ont.md
│   ├── 14_cuellos_botella_operacionales.md
│   ├── 15_iot_organ_tracking_paragonix_unos.md
│   ├── 16_hospital_italiano_hiba_fhir.md
│   ├── 17_workflow_quirurgico_paralelo.md
│   └── 18_renaper_sid_capabilities.md
│
└── analisis/                     ← análisis críticos / propuestas
    ├── 01_propuesta_deteccion_facial_renaper.md
    ├── 02_sid_validacion_pasiva_y_api_dispositivos_firmados.md
    └── 03_sensor_huella_costo_bom.md
```

### Cómo navegar

- **Si querés entender el problema y el ecosistema** → leé `research.md` (síntesis de 16 secciones).
- **Si querés profundizar en una fuente concreta** → entrá a `sources/NN_*.md`.
- **Si querés ver soluciones técnicas analizadas** → entrá a `analisis/NN_*.md`.
- **Si una sigla no la conocés** → `GLOSARIO.md`.
- **Si querés seguir investigando otro ángulo** → leé `WORKFLOW.md` y replicá la metodología.

---

## Hallazgos clave (cheat sheet)

### Ecosistema argentino
- **INCUCAI** = autoridad nacional (Ley 27.447 lo designa).
- **SINTRA** = sistema central de información. Stack 2003 (Linux+Oracle+J2EE). **6 módulos. Sin APIs públicas.**
- **24 CUCAI provinciales** con autonomía operativa.
- **~130 coordinadores hospitalarios** + **28 UHPROT**.
- **Aerolíneas Argentinas** transporte aéreo (~4 envíos diarios; sin API).
- **Casa Justina + H+Trace** ya operan drones autorizados por ANAC.
- **HIBA** (Hospital Italiano) líder regional FHIR (HIMSS EMRAM 7).

### Datos duros
- **4.263 trasplantes** en Argentina 2024.
- **1.972 donantes efectivos** (DPMH 17,7).
- **~11.000 personas en lista de espera**.
- **25-35 % muere en lista** sin trasplantarse.
- **40 % negativa familiar** (vs España 12 %).
- **2 % donación en asistolia** (vs España >40 %).

### Brechas atacables
1. **Subdetección hospitalaria de Glasgow ≤7** — el gap más grande.
2. **Identificación de pacientes NN** — abordado en `analisis/01-03`.
3. **Notificación manual / por teléfono al CUCAI** — automatizable.
4. **Coordinación de operativo** sin timeline compartido en tiempo real.
5. **SINTRA sin APIs** — interoperabilidad limitada con HIS.
6. **Cultura institucional** y conocimiento desigual de la Ley Justina.

---

## Roadmap propuesto (alto nivel)

| Fase | Plazo | Entregable |
|---|---|---|
| **Fase 1 — Research broad** | hecho ✅ | `research.md` + 18 fuentes |
| **Fase 2 — Análisis vertical RENAPER** | hecho ✅ | `analisis/01-03` |
| **Fase 3 — Análisis vertical otros vectores** | próximo | Analizar Glasgow auto-detect, notificación, capacitación |
| **Fase 4 — Diseño de la solución integrada** | mes 2 | Documento de arquitectura + scope MVP |
| **Fase 5 — Prototipo + validación** | mes 3-5 | Demo funcional + entrevistas con coordinadores |
| **Fase 6 — Pitch + presentación AFIDEER** | mes 6 (nov 2026) | Pitch deck + demo |
| **Fase 7 — Post-piloto** | 2027+ | Convenios formales con INCUCAI/RENAPER/Casa Justina |

---

## Cómo contribuir / continuar

1. Leé este README.
2. Leé `WORKFLOW.md` — metodología paso a paso.
3. Elegí un ángulo no analizado (de los "pendientes" arriba).
4. Investigá siguiendo la plantilla de fuente.
5. Sintetizá en un `analisis/NN_*.md`.
6. Actualizá `research.md` con cualquier hallazgo transversal.

### Si vas a usar Claude o un LLM para continuar
Hay prompts probados en `WORKFLOW.md` § 6 que reproducen el estilo de investigación de este repo. Pegá la pregunta de investigación + lista de fuentes a consultar + plantilla a usar.

---

## Contacto e instituciones

| Quién | Para qué | Canal |
|---|---|---|
| **AFIDEER** | Mentoría + intros + reglas del desafío | Vía facultad UAP |
| **Casa Justina** | Sponsor del desafío + validación clínica | https://www.casajustina.org |
| **UAP — Facultad Ciencias de la Salud** | Asesoría clínica | Campus UAP |
| **INCUCAI — Dirección de Sistemas** | API SINTRA | Nota formal vía Casa Justina |
| **RENAPER — Convenios SID** | Acceso facial/huella | conveniosid@renaper.gob.ar + TAD |
| **AAIP** | Dictamen datos personales | https://www.argentina.gob.ar/aaip |
| **CUCAI Entre Ríos** | Piloto provincial | cucai@entrerios.gov.ar (verificar) |

---

## Licencia y uso del material

Este es material de investigación interna del equipo. Las fuentes citadas son públicas y se referencian con URL. Los análisis son originales y propiedad intelectual del equipo UAP / Casa Justina (a definir formalmente con sponsor).

> Si compartís esto fuera del equipo, mencioná el origen y verificá con Casa Justina antes de distribuirlo masivamente.

---

## Próximo paso recomendado

Antes de profundizar en más soluciones técnicas:

1. **Reunión con Casa Justina** — validar si el ángulo RENAPER + identificación NN les resuena, o si prefieren foco en otra dimensión (Glasgow auto-detect, capacitación, etc.).
2. **Entrevista con 1-2 coordinadores hospitalarios** — confirmar el pain point real del día a día.
3. **Decidir si el MVP es vertical** (RENAPER profundo) **o integrado** (3-4 componentes coordinados).

Una vez decidido eso, retomar `WORKFLOW.md` con el ángulo elegido.
