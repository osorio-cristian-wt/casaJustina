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
- **Donante presunto** (>18 años).
- **INCUCAI** = autoridad de aplicación.
- **SINTRA** declarado obligatorio (Art. 50).
- **Trazabilidad** obligatoria (Art. 38).
- **6 horas** máximo desde declaración muerte hasta entrega del cuerpo.
- Cobertura 100% por financiadores (Art. 64).

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
8. **Alertas Glasgow ≤7** integradas a HIS hospitalarios (alerta a coordinador hospitalario).

### B) Experimentales, 1-3 años
1. **ML para predicción de aceptación** — predice qué equipo aceptará el órgano → reordena lista para reducir tiempo de oferta.
2. **ML para predicción de viabilidad** con datos de machine perfusion.
3. **Optimización de routing dinámico** considerando CIT del órgano + tráfico + vuelos disponibles.
4. **Capacitación VR** — simulación de comunicación con familia (Casa Justina ya explora).
5. **Telemetría de máquinas de perfusión** integrada al operativo.
6. **Blockchain/append-only ledger** para trazabilidad inmutable (Art. 38).
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
7. **Auditoría inmutable** — append-only para Art. 38 Ley 27.447.
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

---

## 13. Patrones detectados

1. **Argentina está "una generación atrás" en interoperabilidad** comparado a USA/UK, pero TIENE el marco normativo y técnico FHIR adoptado oficialmente.
2. **Casa Justina es el catalizador de modernización extra-INCUCAI**: drones, AI, VR.
3. **El gap operativo (40% negativa) es mayor que el gap tecnológico**.
4. **La estructura organizacional argentina (3 niveles: nacional/provincial/hospitalario)** copia bien el modelo español; la diferencia es **densidad de coordinadores + calidad de detección**.
5. **SINTRA no tiene "competencia" privada** — toda startup que entre debe hacerlo en colaboración, no en oposición.
6. **Las distancias argentinas (4.000 km) requieren más sofisticación logística que cualquier país europeo** individual.

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
4. [04_ley_27447_justina.md](sources/04_ley_27447_justina.md) — Marco normativo
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

---
