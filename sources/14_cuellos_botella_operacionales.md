# Cuellos de botella operacionales del sistema argentino

- **URLs:**
  - https://www.lanacion.com.ar/comunidad/el-40-de-las-donaciones-de-organos-se-pierden-por-oposicion-familiar-nid2138975/
  - https://www.lanacion.com.ar/salud/como-se-hace-un-trasplante-6-horas-junto-al-equipo-del-hospital-italiano-nid2235513/
  - https://www.scielo.org.ar/scielo.php?script=sci_arttext&pid=S2250-639X2019000400002
- **Fecha de consulta:** 2026-05-14

## Resumen

Compilación de cuellos de botella concretos identificados a través de fuentes oficiales, prensa especializada y literatura médica argentina.

## Cuellos de botella identificados

### 1. Detección subnotificada de potenciales donantes
- **Síntoma:** España detecta 3x más donantes por millón.
- **Causa:** Falta de monitoreo sistemático en UTI argentinas.
- **Hoy es:** Manual, dependiente de coordinador hospitalario.
- **Oportunidad:** Alertas automáticas desde HIS (cuando Glasgow ≤7 + edad apta) → notificación coordinador.

### 2. Negativa familiar 40% (50% pediátricos)
- **Síntoma:** España 12%, Uruguay <1%.
- **Causa:** Cultural + comunicación deficiente.
- **Hoy es:** Conversación cara a cara, no estandarizada.
- **Oportunidad:** Capacitación digital con simulación, scripts validados, análisis de conversación.

### 3. Coordinación de operativo dispersa
- **Síntoma:** Hasta 150 profesionales pueden intervenir; en doble trasplante Garrahan participaron 50+.
- **Causa:** Coordinación por teléfono/WhatsApp/correo.
- **Hoy es:** Coordinador hospitalario hace llamadas en cascada.
- **Oportunidad:** Plataforma de orquestación con timeline compartido en tiempo real, asignación de tareas, status por equipo.

### 4. Oferta secuencial ineficiente
- **Síntoma:** Cada rechazo agrega minutos al CIT.
- **Causa:** SINTRA ofrece a #1, espera, va a #2, espera...
- **Oportunidad:** Pre-screening automático, oferta paralela cuando algoritmo predice rechazo.

### 5. Transporte aéreo dependiente y manual
- **Síntoma:** Depende de Aerolíneas Argentinas (~4 envíos diarios), sin backup nacional.
- **Causa:** Convenio único, sin SLAs digitales, sin redundancia.
- **Hoy es:** Notificación INCUCAI → Aerolíneas, manual.
- **Oportunidad:** API de slots, integración multi-aerolínea, alternativa con jets dedicados/drones.

### 6. Última milla en ciudades grandes
- **Síntoma:** 1-2,5h en taxi/ambulancia Aeroparque-Hospital en CABA (vs 30 min en dron).
- **Causa:** Tráfico urbano.
- **Hoy es:** Ambulancias.
- **Oportunidad:** Drones (proyecto Casa Justina/H+Trace ya en marcha).

### 7. Datos clínicos del receptor desactualizados en SINTRA
- **Síntoma:** Score MELD/PELD/situación renal puede no estar actualizado.
- **Causa:** Carga manual periódica por centros.
- **Hoy es:** El centro carga cuando le acuerda.
- **Oportunidad:** Sincronización automática desde HIS hospitalario vía FHIR.

### 8. Falta de tracking de cadena de frío en tiempo real
- **Síntoma:** Sensores en contenedor no integrados a sistema central.
- **Causa:** Contenedores físicos pasivos.
- **Hoy es:** Termómetros analógicos o digitales aislados.
- **Oportunidad:** Contenedores IoT con telemetría continua.

### 9. Tiempo de evaluación de viabilidad post-isquemia
- **Síntoma:** No siempre se sabe si órgano fue viable hasta que se implanta o se descarta.
- **Causa:** Sin máquinas de perfusión normotérmica generalizadas.
- **Oportunidad:** Programa nacional de perfusión + telemetría de máquinas + ML para predicción.

### 10. Heterogeneidad de procesos entre 24 jurisdicciones
- **Síntoma:** Cada CUCAI provincial opera distinto.
- **Causa:** Federalismo + falta de plataforma común más allá de SINTRA.
- **Oportunidad:** Plataformas SaaS provinciales que normalicen workflows.

### 11. Mortalidad en lista de espera 25-35% anual
- **Síntoma:** 11.000 personas en lista, 10% se trasplantan, 25-35% mueren esperando.
- **Causa:** Combinación de todos los cuellos anteriores.

### 12. SINTRA sin APIs públicas
- **Síntoma:** Imposible integrar con HIS hospitalario o sistemas externos.
- **Causa:** Arquitectura J2EE/Oracle de 2003.
- **Oportunidad:** API gateway adelante de SINTRA con FHIR.

### 13. Donación en asistolia subutilizada
- **Síntoma:** Solo 36 procesos en 2024 (2% del total).
- **Causa:** Protocolo reciente (2023), pocos centros entrenados, infraestructura de perfusión escasa.

### 14. Capacitación dispersa
- **Síntoma:** ~130 coordinadores hospitalarios. Capacitación con esfuerzos puntuales.
- **Oportunidad:** Plataforma LMS nacional + simuladores VR (Casa Justina ya lo explora).

## Relevancia / Implicancias

Todos estos cuellos de botella son **direccionables con tecnología moderna** sin tocar la ley:
- Event-driven architecture para coordinación.
- IoT para tracking de órganos y máquinas de perfusión.
- ML para matching predictivo y predicción de aceptación.
- API gateway FHIR para SINTRA.
- Mobile-first para coordinadores hospitalarios.
- Drones para última milla.
- VR/AR para capacitación.

## Observaciones

- **La oportunidad NO está en reemplazar SINTRA** (sería políticamente imposible). Está en **construir capas modernas alrededor** que lo enriquezcan y conecten con el resto del ecosistema.
- Casa Justina + H+Trace ya están demostrando que esto es posible.
- El gap más grande es probablemente la **detección hospitalaria** — si Argentina pasa de 17 a 30 DPMH, casi duplica los trasplantes anuales.
