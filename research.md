# Investigación profunda — Logística de trasplantes en Argentina

> Documento principal. Síntesis del ecosistema técnico-operacional argentino de procuración y trasplante, con foco en oportunidades de modernización tecnológica.
>
> **Fecha:** 2026-05-14 | **Repositorio:** `casaJustina`

---

## 1. Mapa institucional del ecosistema

```
                ┌───────────────────────────────────────┐
                │   Ministerio de Salud de la Nación    │
                └───────────────┬───────────────────────┘
                                │
                       ┌────────▼─────────┐
                       │     INCUCAI      │   ← Autoridad de aplicación (Ley 27.447)
                       │ (autoridad nac.) │
                       └────────┬─────────┘
                                │
        ┌───────────────────────┼──────────────────────────────┐
        │                       │                              │
   ┌────▼─────┐         ┌───────▼──────┐              ┌────────▼────────┐
   │  SINTRA  │         │ 24 CUCAI/COTAI│              │ COFETRA         │
   │ (J2EE +  │◄────────│ provinciales  │◄─────────────│ (Comisión Fed.) │
   │  Oracle) │         │ (CUDAIO,      │              └─────────────────┘
   └────┬─────┘         │  CUCAIBA,etc) │
        │               └───────┬───────┘
        │                       │
        │              ┌────────▼──────────┐
        │              │ Hospitales        │
        │              │ procuradores      │
        │              │ (UHPROT, ~28 ud.) │
        │              └────────┬──────────┘
        │                       │
        │              ┌────────▼──────────┐
        │              │ Coordinadores     │
        │              │ hospitalarios     │
        │              │ (~130 prof.)      │
        │              └────────┬──────────┘
        │                       │
        │              ┌────────▼──────────┐
        └─────────────►│ Centros de        │
                       │ trasplante (renov.│
                       │ bienal)           │
                       └────────┬──────────┘
                                │
                       ┌────────▼──────────┐
                       │ Bancos de tejidos │
                       │ + Lab HLA         │
                       └───────────────────┘
```

**Actores clave:**
- **INCUCAI** — organismo descentralizado del Ministerio de Salud. Normativo, fiscalizador, operativo nacional.
- **24 CUCAI provinciales** — operan jurisdiccionalmente con autonomía (CUDAIO Santa Fe, CUCAIBA Buenos Aires, CUCAI Chaco, etc).
- **COFETRA** — Comisión Federal de Trasplantes (representantes provinciales coordinados por INCUCAI).
- **Hospitales procuradores** + **UHPROT** (Unidades Hospitalarias de Procuración) — 28 unidades, 12 instaladas en 2024.
- **>130 coordinadores hospitalarios** (típicamente intensivistas).
- **Centros de trasplante** habilitados con renovación bienal.
- **Laboratorios HLA** (tipificación, crossmatch).
- **Bancos de tejidos**.
- **Aerolíneas Argentinas** (convenio 2022, ~4 envíos diarios).
- **Aviación sanitaria provincial** (helicópteros).
- **ANAC** — habilita operaciones aéreas (incluyendo drones).
- **Casa Justina + H+Trace** — proyecto activo de drones para órganos.

---

## 2. Workflow operacional canónico (10 etapas)

| # | Etapa | Actor principal | Sistema | Decisión |
|---|---|---|---|---|
| 1 | Detección potencial donante | UTI | Monitor + HIS local | Humana (Glasgow ≤7 trigger) |
| 2 | Certificación muerte encefálica | Equipo médico | Protocolo Res. 716/19 | Humana (clínica + EEG/Doppler) |
| 3 | Comunicación familiar | Equipo tratante + procurador | Registro voluntad | Humana |
| 4 | Evaluación y selección | Equipo evaluador | SINTRA + laboratorio | Mixta (criterios clínicos) |
| 5 | Mantenimiento donante | UTI | Monitor | Humana |
| 6 | Intervención judicial (si aplica) | Magistrado | — | Humana |
| 7 | Distribución y asignación | INCUCAI / CUCAI | **SINTRA (algoritmo)** | **Automatizada** + aceptación humana |
| 8 | Ablación y preservación | Equipo quirúrgico procurador | Quirófano | Humana |
| 9 | Transporte | Procurador o trasplantista | Aerolínea / ambulancia / dron | Mixta |
| 10 | Acondicionamiento del cuerpo | Procurador | — | Humana |

**Etapa 7 (asignación)** es el único punto **realmente automatizado** del workflow. El resto es coordinación humana asistida por SINTRA como base de datos.

**Duración total típica:** ~48 horas (puede extenderse en donación en asistolia o distribución de tejidos).

---

## 3. SINTRA — el sistema central

### Arquitectura
- **Stack:** Linux + Oracle + J2EE (Java EE).
- **Hosting:** On-premise en facility INCUCAI.
- **Acceso:** Web, sin instalación local, sesión cifrada.
- **Disponibilidad:** 24/7/365.
- **Origen:** Desarrollo inició 2003.

### 6 módulos
1. **Registro Nacional de IRCT** (Insuficiencia Renal Crónica Terminal — pacientes en diálisis).
2. **Listas de espera** (receptores activos).
3. **Registro Nacional de Procuración** (operativos).
4. **Registro Nacional de Trasplante** (implantes).
5. **Registro Nacional de Donantes de Órganos y Tejidos** (voluntad).
6. **Registro Nacional de Donantes CPH** (única conexión internacional documentada: BMDW/WMDA).

**Sistema relacionado: CRESI** — registro de instituciones, equipos, bancos.

### Limitaciones observadas
- **Sin APIs públicas documentadas.** Toda integración con HIS es manual.
- **Sin soporte FHIR/HL7 declarado.** No interopera con la Red Nacional de Salud Digital.
- **Stack 2003** — probable arquitectura monolítica, sin event streaming, sin tiempo real "verdadero".
- **Carga manual de datos** redundante (el coordinador hospitalario carga lo que ya está en su HIS).
- **No emite notificaciones push** en tiempo real a equipos receptores (oferta es por SINTRA + llamada).

### Fortalezas
- Base de datos única consistente para todo el país (single source of truth legal).
- 22 años de operación continua — sistema probado.
- Cubre el workflow completo end-to-end.

---

## 4. Marco normativo

### Ley 27.447 — "Ley Justina" (2018)
> Articulado completo y verificado en [sources/04](sources/04_ley_27447_justina.md) (fuente canónica). Numeración corregida abajo.
- **Donante presunto**, mayor de 18 años (**Art. 33**).
- **Deber de detección** del donante en los establecimientos (**Art. 14**) y **deber de iniciar el proceso de donación** al certificar la muerte (**Art. 39 — "Notificación"**).
- **INCUCAI** = organismo rector (**Art. 56**) con 30 funciones (**Art. 57**), entre ellas **trazabilidad** (inc. 1) y el **sistema nacional de información** (inc. 29, base normativa de SINTRA — la ley no nombra "SINTRA").
- **Gastos** no a cargo del dador; los cubre el financiador del receptor (**Art. 28**).
- **COFETRA** dentro del capítulo del INCUCAI (**Art. 57 + Art. 61**).

### Resoluciones operacionales clave
- **Res. 716/19** — Protocolo de certificación de muerte.
- **Res. 327/2023** — Protocolo de donación en asistolia (Maastricht).
- **Res. 82/22 + 128/25** — Distribución hepática.
- **Res. 1642/22** — Perfil del coordinador hospitalario.
- **Res. 680/2018 (MS)** — Estándares de salud digital (FHIR, SNOMED CT).

### Implicancia clave
La normativa **NO prohíbe** APIs sobre SINTRA, ni integración FHIR, ni adopción de IoT. Es un marco habilitante. **Lo que falta es ejecución técnica.**

---

## 5. Algoritmos de asignación

| Órgano | Algoritmo |
|---|---|
| Riñón | Score por sumatoria (antigüedad diálisis + inmunológico + ABO/HLA + región) |
| Hígado | MELD-Na (≥12 años) / PELD (<12 años) + categoría emergencia + pediátrico + lista hepatointestinal |
| Corazón / Pulmones | Urgencia clínica + compatibilidad + tiempo en lista (subcomisiones) |
| Córneas | Antigüedad regional |

### Comparación con UNOS / NHS / Eurotransplant

| Aspecto | INCUCAI/SINTRA | UNOS | NHS BT | Eurotransplant |
|---|---|---|---|---|
| Match engine | Score nativo | UNet match run | IBM ODM rules engine | ETKAS |
| Transparencia algoritmica | Resoluciones públicas | OPTN public comment | UK Algorithmic Transparency Standard | Publicaciones científicas |
| APIs públicas | **No documentadas** | **238 APIs / 300+ implementaciones** | Internas | Limitadas |
| Integración EHR | No | Epic Phoenix, Cerner | Parcial | Parcial |
| FHIR | No | Evaluación | No | No |
| Cloud | On-premise | Google Cloud | — | On-premise |

**Brecha clara:** Argentina es **el sistema más cerrado** de los cuatro en términos de APIs e integraciones con HIS.

---

## 6. Logística operativa

### Transporte aéreo
- **Aerolíneas Argentinas** — único partner aéreo nacional (convenio 2022).
- ~4 envíos diarios.
- Vuelos sanitarios con prioridad en ruta y aterrizaje.
- **Notificación INCUCAI→Aerolíneas es manual** (sin API documentada).
- **Sin redundancia** ante problemas operativos de Aerolíneas.

### Transporte terrestre
- Ambulancias provinciales.
- Aviación sanitaria provincial (helicópteros).
- Sin tracking integrado a SINTRA.

### Última milla
- **Cuello de botella crítico:** Aeroparque → Hospital El Cruce (32 km) = 1-2.5h por tráfico.
- **Drones (H+Trace + Casa Justina)** demostrado que reducen a <30 min, **8x más barato**.

### Cadena de frío
- Contenedores pasivos a 4°C con soluciones (UW, Custodiol, Celsior).
- **Sin telemetría centralizada** (a diferencia de UNOS Organ Tracking Service o Paragonix).

### Tiempos críticos por órgano

| Órgano | CIT máximo | Implicancia geográfica AR |
|---|---|---|
| Corazón | 4–6h | Solo intra-CABA o vuelo directo |
| Pulmones | 4–6h | Idem |
| Hígado | 8–12h | Vuelo + ambulancia OK |
| Páncreas | 12h | Vuelo + ambulancia |
| Riñón | 18–36h | Vuelo / colectivo / tren |
| Córneas | Días (medio especial) | Sin urgencia |

### Cinética temporal de viabilidad — del umbral estático al modelo dinámico

Los CIT publicados son **umbrales binarios** ("4–6 h y listo") que ocultan dos hechos relevantes para la logística:

1. **El reloj no arranca con la extracción**, sino con el **paro circulatorio** del donante. Cada minuto previo a la cardioplegia a temperatura corporal cuenta multiplicado.
2. **La viabilidad decae exponencialmente** con dependencia térmica tipo Van't Hoff / Q₁₀. Pequeñas desviaciones del perfil térmico esperado pueden recortar el margen disponible de forma desproporcionada.

Un modelo de decaimiento de primer orden calibrado con la literatura clínica (k_ref ≈ 1.39 h⁻¹ a 37 °C, Q₁₀ ≈ 2.12) reproduce los tiempos estándar y permite cuantificar:

| Escenario | Impacto en tiempo útil de transporte |
|---|---|
| 15 min de WIT a 25 °C (paro previo a cardioplegia) | **−1.21 h** vs. cardioplegia inmediata (~20 % del presupuesto consumido) |
| Falla térmica suave en contenedor (4 °C → 8 °C) | **−26 %** del tiempo útil |
| Falla térmica grave (4 °C → 25 °C) | **−80 %** del tiempo útil |

**Conclusión transversal:** saber sólo "el corazón aguanta 4–6 h" no es accionable para logística. Lo accionable es el **tiempo equivalente a 4 °C**, calculado en tiempo real a partir del perfil térmico medido. Esto refuerza el caso para telemetría IoT en contenedor (cuello de botella #8) y para un módulo de viabilidad dinámica sobre el orquestador (sec. 10).

Análisis cuantitativo completo, calibración, ejemplos encadenados y limitaciones: [analisis/04_modelo_dinamico_viabilidad_cardiaca.md](analisis/04_modelo_dinamico_viabilidad_cardiaca.md).

---

## 7. Datos y estadísticas (2024)

- **4.263 trasplantes** realizados.
- **1.972 procesos de donación**.
- **DPMH órganos: 17,7** (vs España 51,9).
- **DPMH tejidos: 24** (récord histórico).
- **Donación en asistolia: 36 procesos** (2% — vs España >40%).
- **28 UHPROT** instaladas.
- **>130 coordinadores hospitalarios**.
- **~11.000 personas** en lista de espera.
- **Mortalidad en espera: 25-35% anual**.
- **Rechazo familiar: 40% (50% pediátricos)** — vs España 12%, Uruguay <1%.

**Si Argentina alcanzara DPMH 30 → ~3.300 donantes → ~7.000 trasplantes. Si alcanzara DPMH español (52) → ~5.000 donantes → ~10.000 trasplantes. La oportunidad humana es enorme.**

---

## 8. Cuellos de botella (priorizados por impacto)

| # | Cuello de botella | Causa raíz | Tecnología que ayuda | Barrera |
|---|---|---|---|---|
| 1 | **Subdetección de potenciales donantes** | UTI no reporta sistemáticamente | Alertas automáticas desde HIS (Glasgow ≤7) | Adopción HIS heterogénea |
| 2 | **40% negativa familiar** | Cultural + comunicación | Capacitación digital, scripts, VR | Cambio cultural |
| 3 | **Coordinación operativo dispersa** | Teléfono/WhatsApp en cascada | Plataforma orquestación con timeline compartido | Cambio de hábito |
| 4 | **Oferta secuencial lenta** | SINTRA ofrece uno a uno | Pre-screening + ofertas paralelas | Regulatoria menor |
| 5 | **Transporte aéreo manual** | Sin API entre INCUCAI ↔ Aerolíneas | API + slot booking + multi-aerolínea | Comercial |
| 6 | **Última milla urbana** | Tráfico CABA/AMBA | **Drones (H+Trace ya operando)** | Regulación BVLOS |
| 7 | **Datos receptor desactualizados** | Carga manual periódica | Sync FHIR HIS → SINTRA | Adopción HIS |
| 8 | **Sin tracking IoT cadena frío** | Contenedores pasivos | Contenedores IoT estándar (ver [analisis/04](analisis/04_modelo_dinamico_viabilidad_cardiaca.md): el perfil térmico real puede recortar hasta 26 % del CIT útil sin que nadie lo note) | Costo + estandarización |
| 9 | **Evaluación viabilidad post-isquemia** | Pocas máquinas perfusión | Programa nacional + telemetría perfusión | Capital + capacitación |
| 10 | **Heterogeneidad 24 CUCAI** | Federalismo | SaaS provincial común | Política federal |
| 11 | **SINTRA sin APIs** | Stack 2003 | API gateway FHIR sobre SINTRA | Política institucional |
| 12 | **Donación en asistolia subutilizada** | Protocolo reciente, centros sin entrenamiento | Capacitación + perfusión | Capacitación |
| 13 | **Capacitación dispersa** | Esfuerzos puntuales | LMS + simuladores VR | Adopción |
| 14 | **Auditoría reactiva** | Reportes manuales | Observabilidad continua | Cultura |

### Profundización del cuello #1 — Subdetección: el criterio ya existe, falta ejecutarlo

La investigación del vector "Alertas Glasgow ≤7 desde HIS" arrojó un hallazgo que reencuadra este cuello de botella: **el disparador clínico ya está normado en Argentina**. El **Subprograma de Garantía de Calidad del INCUCAI se basa explícitamente en la detección y monitoreo de pacientes neurocríticos con Glasgow ≤7 (PSG<7)** ([sources/19](sources/19_incucai_deteccion_psg7_garantia_calidad.md)), y la Ley 27.447 Art. 14 obliga a los establecimientos a tener servicios que permitan "la correcta **detección**, evaluación y tratamiento del donante" ([sources/26](sources/26_marco_legal_deteccion_premortem.md)). El gap, entonces, **no es de definición sino de ejecución**: hoy ese monitoreo depende de que un humano lea fichas o planillas.

- **Evidencia de impacto:** automatizar la derivación por triggers (ventilación + UCI neurocrítica + Glasgow) desde el EHR aumentó en un estudio los donantes efectivos **+92 %** (referrals +45 %, autorizaciones +73 %) en hospitales piloto de Texas ([sources/21](sources/21_automatizacion_referral_ehr_estudio.md)).
- **Detección pre-mortem:** el referral ocurre **antes de la muerte** (NHS: Glasgow ≤4 + reflejos ausentes, o decisión de WLST), para que el referente —en Argentina, el **coordinador hospitalario**— evalúe y siga al paciente sin intervenir en su cuidado ([sources/20](sources/20_triggers_clinicos_premortem_nhs_usa.md)).
- **Matiz técnico clave:** el Glasgow es una **valoración asistencial registrada en el HIS** (lo carga enfermería), **no** un dato que emita el monitor de cabecera → el adaptador debe leer del HIS/HCE vía FHIR, no del monitor ([sources/23](sources/23_monitores_uti_captura_datos.md)).

Análisis crítico completo (3 arquitecturas, costos, legal, plan): [analisis/05_alertas_glasgow_his_hospitalarios.md](analisis/05_alertas_glasgow_his_hospitalarios.md).

### Profundización de la etapa 2 — Disparar la logística al certificarse la muerte encefálica

La detección PSG<7 es la etapa 1 (pre-mortem). La **etapa 2** es el segundo evento crítico: el instante en que se **certifica la muerte encefálica** según el Protocolo Nacional (Res. 716/2019, [sources/27](sources/27_resolucion_716_2019_muerte_encefalica.md)). Ese acto médico —humano, no automatizable— es la **frontera legal y operativa**: a partir de ahí opera la presunción de donación (Ley 27.447 Art. 33, **sin consentimiento familiar para adultos**, [sources/04](sources/04_ley_27447_justina.md)) y el Art. 39 ("Notificación") obliga a iniciar el proceso. Dos cosas deben pasar automáticamente:

- **Avisar a todos (coordinador + CUCAI + logística) en minutos, no horas.** Hoy es manual/telefónico (cuello #3). Evidencia: un CDS automatizado bajó el tiempo de notificación de **30,2 h a 1,7 h** y aumentó donantes, con pocos falsos positivos y mínima interrupción ([sources/31](sources/31_cds_muerte_encefalica_inminente_zier.md)). El benchmark regulatorio de USA (CMS 42 CFR 482.45) exige avisar **≤1 h y antes de retirar soporte vital** ([sources/29](sources/29_required_referral_cms_42cfr48245.md)).
- **Que el equipo sepa que NO debe desconectar.** Tras la muerte encefálica el objetivo clínico cambia de "tratar la presión intracraneal" a "mantener la perfusión de los órganos"; **hasta el 20 % de los órganos se pierden por mal manejo hemodinámico del donante** ([sources/30](sources/30_manejo_donante_muerte_encefalica_no_desconectar.md)). El aviso debe traer las metas de mantenimiento del donante (DMG), no ser sólo administrativo.

Argentina **no tiene un "required referral" explícito** como USA; la oportunidad es **implementarlo técnicamente** apoyándose en los Art. 14/39 ya vigentes. Análisis completo: [analisis/06_disparo_logistica_muerte_encefalica.md](analisis/06_disparo_logistica_muerte_encefalica.md).

---

## 9. Oportunidades tecnológicas (separadas por madurez)

### A) Realistas, ejecutables 6-18 meses
1. **API gateway FHIR sobre SINTRA** — adaptador read/write con recursos FHIR mapeados a entidades SINTRA. No requiere tocar SINTRA core.
2. **Mobile app para coordinadores hospitalarios** — alta de operativo, checklist, notificaciones push.
3. **Contenedores IoT homologados** — temp/shock/GPS, integrados a un backend nacional (puede ser de Casa Justina).
4. **Timeline compartido del operativo** — webapp + push, con todos los actores viendo estado en tiempo real.
5. **Drones para última milla** — escalar piloto H+Trace a 7 provincias.
6. **Sync HIS-HIBA → SINTRA via FHIR** — piloto con un hospital top.
7. **Dashboards operacionales públicos** — métricas en tiempo real (DPMH, operativos, etc.).
8. **Alertas Glasgow ≤7 desde HIS** → alerta al **coordinador hospitalario** (no al intensivista, para evitar alert fatigue). Detección **pre-mortem** vía FHIR Subscription (`Observation` LOINC 9269-2, filtro valor ≤7) cuando el HIS lo soporta, o polling/HL7 v2 como fallback. Integración 1-a-muchos con **HSI** (open source, +1.800 efectores) la vuelve escalable. Analizado a fondo en [analisis/05](analisis/05_alertas_glasgow_his_hospitalarios.md) — es el vector que reencuadra el cuello #1 como problema de ejecución, con evidencia de **+92 % donantes** ([sources/21](sources/21_automatizacion_referral_ehr_estudio.md)).
9. **Auto-disparo de logística al certificarse la muerte encefálica** (etapa 2) → al registrarse la certificación (Res. 716/2019) en el HIS, notificación automática al coordinador + CUCAI + equipos (objetivo ≤1 h, como CMS USA), con el mensaje clínico de **mantener al donante (DMG), no desconectar**. Evidencia: notificación de 30,2 h → 1,7 h ([sources/31](sources/31_cds_muerte_encefalica_inminente_zier.md)). Encadena con el ítem 8. Analizado en [analisis/06](analisis/06_disparo_logistica_muerte_encefalica.md).

### B) Experimentales, 1-3 años
1. **ML para predicción de aceptación** — predice qué equipo aceptará el órgano → reordena lista para reducir tiempo de oferta.
2. **ML para predicción de viabilidad** con datos de machine perfusion.
3. **Optimización de routing dinámico** considerando CIT del órgano + tráfico + vuelos disponibles.
4. **Capacitación VR** — simulación de comunicación con familia (Casa Justina ya explora).
5. **Telemetría de máquinas de perfusión** integrada al operativo.
6. **Blockchain/append-only ledger** para trazabilidad inmutable (Art. 57 inc. 1 — función de trazabilidad del INCUCAI).
7. **UTM (Unmanned Traffic Management)** nacional para drones sanitarios.

### C) Barreras y bloqueos
- **Regulatoria**: BVLOS en drones, modificaciones de algoritmo allocación, compartir datos identificables.
- **Política**: INCUCAI puede ver con sospecha actores externos sobre SINTRA. La estrategia debe ser "complemento, no reemplazo".
- **Cultural**: Adopción de HIS hospitalario es heterogénea. PBA va con HSI/FHIR; otras provincias atrás.
- **Financiera**: Recursos públicos limitados. ONG (Casa Justina) y sector privado pueden complementar.
- **Federal**: 24 jurisdicciones con autonomía. Cualquier cambio nacional requiere COFETRA.

---

## 10. Arquitectura conceptual moderna (propuesta)

```
   ┌───────────────────────────────────────────────────────────────────┐
   │                    Layer 1 — INTEGRATION                          │
   │   FHIR API Gateway  │  Event Bus (Kafka/NATS)  │  IoT Telemetry  │
   └─────────┬───────────────────┬─────────────────────────┬────────────┘
             │                   │                         │
   ┌─────────▼────────┐ ┌────────▼────────┐ ┌──────────────▼─────────┐
   │  SINTRA (legacy) │ │  Workflow       │ │  Tracking & Telemetry  │
   │  Oracle + J2EE   │ │  Orchestrator   │ │  TimescaleDB / Influx  │
   │  (sin tocar)     │ │  (Temporal.io)  │ │                        │
   └──────────────────┘ └─────────────────┘ └────────────────────────┘
             │                   │                         │
             ▼                   ▼                         ▼
   ┌────────────────────────────────────────────────────────────────────┐
   │                    Layer 2 — APPLICATIONS                          │
   │  Mobile app      Web dashboard   Public tracker   ML services      │
   │  (coordinador)   (CUCAI/hosp)    (paciente)       (matching/ETA)   │
   └────────────────────────────────────────────────────────────────────┘
             │                   │                         │
             ▼                   ▼                         ▼
   ┌────────────────────────────────────────────────────────────────────┐
   │                    Layer 3 — ECOSYSTEM                             │
   │  HIS hospitales (FHIR)  │  Aerolíneas (API)  │  Drones (H+Trace)  │
   │  Máquinas perfusión     │  Contenedores IoT  │  Bancos de tejidos │
   └────────────────────────────────────────────────────────────────────┘
```

### Principios de diseño
1. **No reemplazar SINTRA** — actuar como capa de orquestación encima.
2. **Event-driven** — todo evento operacional es un mensaje en el bus.
3. **FHIR nativo** — todos los recursos clínicos son recursos FHIR.
4. **Mobile-first** — coordinadores trabajan desde celular, no escritorio.
5. **Open data interno** — todos los actores ven el mismo timeline.
6. **Public-facing limitado** — paciente/familia puede ver estado de su operativo sin comprometer privacidad.
7. **Auditoría inmutable** — append-only para la trazabilidad de la Ley 27.447 (Art. 57 inc. 1).
8. **Federal-friendly** — cada provincia puede tener su instancia con datos federados.

---

## 11. Hipótesis estratégicas

### H1: La principal palanca de impacto NO es tecnológica
Si Argentina mejora detección hospitalaria + reduce negativa familiar de 40% a 20%, los trasplantes crecen ~50% sin tocar SINTRA. La tecnología es **enabler** del cambio operativo.

### H2: La capa más valiosa para construir es la de orquestación
SINTRA hace bien su trabajo de "registro y matching". Lo que falta es la **capa de coordinación operacional en tiempo real**. Es la oportunidad técnica más concreta.

### H3: Casa Justina es el partner natural
Es la única ONG con:
- Reconocimiento institucional alto (Ley Justina).
- Proyecto innovador activo (drones).
- Acceso a hospitales y INCUCAI.
- Conexiones con startups (H+Trace).
- Capacidad de levantar capital.

Cualquier iniciativa tecnológica seria en este espacio debería co-construirse con Casa Justina.

### H4: HIBA es el partner técnico natural
Hospital Italiano (CABA) lleva 25 años en FHIR, está en EMRAM 7, y es centro de trasplante grande. Piloto técnico aquí baja riesgo.

### H5: La donación en asistolia es la línea de mayor crecimiento
- España 40% asistolia → Argentina 2% asistolia.
- 20x margen de crecimiento.
- Requiere tecnología: máquinas de perfusión, telemetría, coordinación más fina.
- Es la línea donde la tecnología tiene mayor ROI clínico.

### H6: El "4–6 h del corazón" como umbral estático es un dato pobre para decidir logística
La viabilidad cardíaca decae exponencialmente con dependencia térmica desde el paro circulatorio — no desde la extracción. Quince minutos de WIT a 25 °C consumen ~20 % del presupuesto total; una falla térmica suave de 4 °C → 8 °C en transporte recorta ~26 % más. Saber "tenés 6 h" sin telemetría no alcanza: lo accionable es el **tiempo equivalente a 4 °C** recalculado en cada evento del operativo. Esto convierte la telemetría IoT en contenedor en un dato **clínicamente útil**, no sólo en un dato de auditoría. Detalle en [analisis/04_modelo_dinamico_viabilidad_cardiaca.md](analisis/04_modelo_dinamico_viabilidad_cardiaca.md).

---

## 12. Inconsistencias y datos a verificar

- Diferencia entre `sintra.incucai.gov.ar` y `sintra.incucai.gob.ar` — ambos URLs aparecen, sugieren migración de dominio.
- Estimación de mortalidad en lista (25-35%) tiene rango amplio — dato debería pedirse directamente a INCUCAI.
- DPMH España 51,9 es de 2025; INCUCAI 17,7 es 2024 — comparar mismos años.
- El término "Calipso" como ERP aparece en algunos resultados; podría ser el sistema administrativo de INCUCAI, no SINTRA.
- El número de coordinadores hospitalarios (~130) vs hospitales totales del país sugiere baja densidad operativa.
- No se encontró documentación pública sobre la próxima generación de SINTRA o planes de modernización.
- **Vector Glasgow (a verificar antes del piloto):** versión FHIR de HSI (R4/R4B) y si soporta `Subscription`/webhooks o sólo API de lectura; si el Glasgow se guarda como `Observation` **estructurado** o como **texto libre** en la evolución (define si el filtro automático es viable); y la **frecuencia real de registro de Glasgow** en UTIs argentinas (define la latencia de detección). Detalle en [sources/22](sources/22_fhir_subscription_codificacion_glasgow.md), [sources/24](sources/24_hsi_historia_salud_integrada.md), [sources/23](sources/23_monitores_uti_captura_datos.md).
- **El detalle fino del Subprograma de Garantía de Calidad** (instrumento, frecuencia exigida, reporte) no es público; requiere anexos de la Res. 199/04 o pedido formal al INCUCAI vía Casa Justina.

---

## 13. Patrones detectados

1. **Argentina está "una generación atrás" en interoperabilidad** comparado a USA/UK, pero TIENE el marco normativo y técnico FHIR adoptado oficialmente.
2. **Casa Justina es el catalizador de modernización extra-INCUCAI**: drones, AI, VR.
3. **El gap operativo (40% negativa) es mayor que el gap tecnológico**.
4. **La estructura organizacional argentina (3 niveles: nacional/provincial/hospitalario)** copia bien el modelo español; la diferencia es **densidad de coordinadores + calidad de detección**.
5. **SINTRA no tiene "competencia" privada** — toda startup que entre debe hacerlo en colaboración, no en oposición.
6. **Las distancias argentinas (4.000 km) requieren más sofisticación logística que cualquier país europeo** individual.
7. **El criterio de detección ya está normado; el cuello es de ejecución, no de definición.** Argentina (PSG<7), NHS (Glasgow ≤4 + DBI) y el modelo EHR de USA (Glasgow ≤5 + vent + UCI) coinciden en disparar la búsqueda de donantes por un Glasgow bajo en paciente neurocrítico **antes de la muerte**. Argentina ya lo exige (Ley 27.447 Art. 14 + Subprograma de Calidad), pero lo ejecuta a mano → el aporte tecnológico es automatizar lo que la norma ya pide, no proponer una práctica nueva. Esto **baja el riesgo regulatorio** del proyecto.
8. **La detección pre-mortem no es invasiva si respeta el desacople ("decoupling"):** la herramienta detecta y avisa al referente de donación; **no** dispara conversación con la familia ni decisiones clínicas. Ese límite es lo que la vuelve legal y clínicamente aceptable.
9. **El destinatario correcto de la alerta es el coordinador de donación, no el médico de cabecera** — patrón confirmado por la literatura de alert fatigue: alertar al intensivista genera override habitual; al coordinador le llega como su propia lista de trabajo.
10. **El proceso tiene dos eventos-disparador, no uno:** (a) **Glasgow ≤7 pre-mortem** → vigilar (etapa 1) y (b) **certificación de muerte encefálica** → disparar logística + mantener al donante (etapa 2). La máquina detecta y notifica en ambos; **las decisiones de certificar la muerte y de hablar con la familia siguen siendo humanas**. La muerte encefálica es la **frontera legal** que habilita pasar de "vigilar dentro del hospital" a "lanzar la coordinación externa".
11. **"No desconectar" es un problema clínico real, no sólo de aviso:** tras la muerte encefálica hasta el **20 % de los órganos se pierden por mal manejo del donante**; el cambio de objetivo (de tratar la presión intracraneal a mantener la perfusión) debe comunicarse activamente al equipo. USA lo fuerza por ley (avisar antes de retirar soporte); Argentina puede implementarlo técnicamente sobre los Art. 14/39 ya vigentes.

---

## 14. Próximos pasos para profundizar (research roadmap)

1. **Acceder a documentación interna SINTRA** (manuales, esquemas, APIs si las hubiera) — solicitar a INCUCAI vía pedido formal.
2. **Entrevistar coordinadores hospitalarios** (Hospital Italiano, Argerich, Garrahan, Favaloro).
3. **Profundizar conversación con H+Trace** sobre integraciones técnicas.
4. **Estudiar Resolución 1642/22** completa (perfil coordinador).
5. **Acceder a estadísticas trimestrales** de INCUCAI por jurisdicción.
6. **Documentar workflow de donación en asistolia** en detalle.
7. **Evaluar HSI Buenos Aires** como referente para integración FHIR.
8. **Analizar UNOS Organ Tracking Service** como benchmark de funcionalidad.

---

## 15. Fuentes consultadas

Cada fuente está documentada en `sources/`:

1. [01_incucai_argentina_gob.md](sources/01_incucai_argentina_gob.md) — INCUCAI institucional
2. [02_sintra_modulos_arquitectura.md](sources/02_sintra_modulos_arquitectura.md) — SINTRA técnico
3. [03_pasos_operativos_trasplante.md](sources/03_pasos_operativos_trasplante.md) — Workflow 10 etapas
4. [04_ley_27447_justina.md](sources/04_ley_27447_justina.md) — **Ley 27.447 completa (fuente canónica del articulado; absorbe la ex-fuente 28)**
5. [05_tiempos_isquemia.md](sources/05_tiempos_isquemia.md) — CIT por órgano
6. [06_aerolineas_convenio.md](sources/06_aerolineas_convenio.md) — Transporte aéreo
7. [07_matching_listas_espera.md](sources/07_matching_listas_espera.md) — Algoritmos asignación
8. [08_interoperabilidad_fhir_argentina.md](sources/08_interoperabilidad_fhir_argentina.md) — FHIR/HL7 nacional
9. [09_comparacion_internacional_unos_eurotransplant_nhs.md](sources/09_comparacion_internacional_unos_eurotransplant_nhs.md) — Benchmarks internacionales
10. [10_donacion_asistolia_resolucion_327_2023.md](sources/10_donacion_asistolia_resolucion_327_2023.md) — Maastricht/DCD
11. [11_estadisticas_argentina_2024_2025.md](sources/11_estadisticas_argentina_2024_2025.md) — Datos cuantitativos
12. [12_casa_justina_drones_htrace.md](sources/12_casa_justina_drones_htrace.md) — Innovación drones
13. [13_modelo_espanol_ont.md](sources/13_modelo_espanol_ont.md) — Modelo español
14. [14_cuellos_botella_operacionales.md](sources/14_cuellos_botella_operacionales.md) — Pain points
15. [15_iot_organ_tracking_paragonix_unos.md](sources/15_iot_organ_tracking_paragonix_unos.md) — IoT estado del arte
16. [16_hospital_italiano_hiba_fhir.md](sources/16_hospital_italiano_hiba_fhir.md) — HIBA y FHIR
17. [17_workflow_quirurgico_paralelo.md](sources/17_workflow_quirurgico_paralelo.md) — Coordinación de equipos
18. [18_renaper_sid_capabilities.md](sources/18_renaper_sid_capabilities.md) — RENAPER SID (identificación biométrica)
19. [19_incucai_deteccion_psg7_garantia_calidad.md](sources/19_incucai_deteccion_psg7_garantia_calidad.md) — Detección PSG<7 + Subprograma de Calidad
20. [20_triggers_clinicos_premortem_nhs_usa.md](sources/20_triggers_clinicos_premortem_nhs_usa.md) — Triggers pre-mortem (NHS + EHR USA)
21. [21_automatizacion_referral_ehr_estudio.md](sources/21_automatizacion_referral_ehr_estudio.md) — Evidencia: +92 % donantes automatizando referral
22. [22_fhir_subscription_codificacion_glasgow.md](sources/22_fhir_subscription_codificacion_glasgow.md) — FHIR Subscription + LOINC 9269-2 (mecánica del adaptador)
23. [23_monitores_uti_captura_datos.md](sources/23_monitores_uti_captura_datos.md) — Monitores UTI y por qué el Glasgow no sale del monitor
24. [24_hsi_historia_salud_integrada.md](sources/24_hsi_historia_salud_integrada.md) — HSI (HIS público open source, +1.800 efectores)
25. [25_alert_fatigue_cds_uti.md](sources/25_alert_fatigue_cds_uti.md) — Alert fatigue: diseño de la alerta
26. [26_marco_legal_deteccion_premortem.md](sources/26_marco_legal_deteccion_premortem.md) — Marco legal pre-mortem (Ley 27.447 / 26.529 / 25.326)
27. [27_resolucion_716_2019_muerte_encefalica.md](sources/27_resolucion_716_2019_muerte_encefalica.md) — Protocolo de certificación de muerte encefálica (disparador etapa 2)
29. [29_required_referral_cms_42cfr48245.md](sources/29_required_referral_cms_42cfr48245.md) — "Required referral" CMS (USA): avisar antes de desconectar
30. [30_manejo_donante_muerte_encefalica_no_desconectar.md](sources/30_manejo_donante_muerte_encefalica_no_desconectar.md) — Mantenimiento del donante: por qué no desconectar (DMG, −20 % órganos)
31. [31_cds_muerte_encefalica_inminente_zier.md](sources/31_cds_muerte_encefalica_inminente_zier.md) — Evidencia: notificación 30,2 h → 1,7 h (Zier et al., AJT 2017)

---
