# Organ Tracking IoT — Estado del arte global

- **URLs:**
  - https://unos.org/solutions/organ-tracking/
  - https://unos.org/organ-tracking-features/
  - https://www.paragonixtechnologies.com/digital-monitoring-and-tracking
  - https://www.mdpi.com/2673-4591/58/1/16
  - https://pmc.ncbi.nlm.nih.gov/articles/PMC12223096/
  - https://multisync.io/opo/tracking
- **Fecha de consulta:** 2026-05-14

## Resumen

A nivel global, el tracking de órganos con GPS + IoT (temperatura, humedad, vibración, orientación, luz) ya es producto comercial estándar. UNOS, Paragonix y MultiSync ofrecen soluciones operacionales. Investigación académica avanza en blockchain + IPFS para trazabilidad inmutable.

## Tecnologías y proveedores

### UNOS Organ Tracking Service
- Servicio comercial integrado con UNet.
- GPS + telemetría.
- Pings cada 2 minutos.
- Cadena de custodia logged.
- ETA predictions.
- Integrado con TIEDI y DonorNet.

### Paragonix Technologies
- Líder en contenedores de preservación inteligentes (SherpaPak para corazón, LIVERguard, etc.).
- Telemetría: temperatura, presión, GPS, alertas.
- Integración con plataformas de OPOs y centros de trasplante.

### MultiSync.io
- Plataforma SaaS para OPOs USA.
- Real-time tracking + integración con OPO Workflow.

### Investigación académica
- **Smart traceable framework con IoT + IPFS + smart contracts** (PMC 12223096):
  - IoT sensors en contenedores.
  - IPFS para almacenamiento descentralizado.
  - Smart contracts para alerta de violaciones.
  - Notificación automática al equipo médico + driver.
- **IoT-based medical container con UAV** (MDPI):
  - Monitoreo: temp, humedad, luz, orientación, vibración.
  - Diseño para drones.

## Parámetros monitoreados (consenso de industria)

| Parámetro | Sensor | Por qué importa |
|---|---|---|
| **Temperatura** | Termistor digital | CIT depende de mantener 4°C. |
| **Humedad** | Sensor capacitivo | Indicador de hielo seco/derretido. |
| **Vibración / Shock** | Acelerómetro | Hígado/pulmón sensibles a vibración. |
| **Orientación** | IMU | Riñones idealmente verticales. |
| **GPS** | Módulo GNSS | Ubicación + ETA. |
| **Apertura** | Reed switch | Manipulación / chain of custody. |
| **Luz** | Fotodiodo | Indica apertura no autorizada. |
| **Presión (perfusión)** | Sensor diferencial | Para machine perfusion. |

## Relevancia para Argentina

### Estado actual
- H+Trace ya usa contenedores con sensores (temp, shock, humedad) para sus drones.
- **No hay sistema centralizado** que consolide telemetría de todos los operativos.
- INCUCAI no ofrece tracking público al receptor o familia.

### Oportunidad
1. Contenedores IoT estándar nacionales (homologados por INCUCAI).
2. Plataforma de tracking integrada con SINTRA.
3. APIs para que centros de trasplante reciban telemetría en tiempo real.
4. Public-facing tracking limitado (estilo "su órgano está a 30 min").
5. Auditoría inmutable (blockchain o append-only ledger) — útil para Art. 38 trazabilidad de Ley 27.447.

## Implicancias tecnológicas

### Stack típico (referencia internacional)
- LTE-M / NB-IoT en zonas urbanas; satelital backup en rutas largas.
- MQTT para telemetría → cloud broker.
- Time-series DB (InfluxDB, TimescaleDB, AWS Timestream).
- Dashboards en tiempo real (Grafana o custom).
- Alertas vía SMS / push / WebRTC.
- Integración API con sistema central (SINTRA en AR).

### Para Argentina específicamente
- Cobertura LTE-M razonable en corredores principales, deficiente en interior profundo (NEA, NOA, Patagonia).
- Satelital (Iridium, Starlink) viable para corredor sur Patagónico.
- Costos: ~USD 200/contenedor + ~USD 10/mes telemetría → << costo helicóptero.

## Citas relevantes

> "GPS-enabled tracking devices ping once every two minutes, providing visibility into the package's progress." (UNOS)

> "IoT sensors embedded in organ containers monitor critical parameters such as temperature, humidity, light intensity, orientation, and vibration." (MDPI research)

## Observaciones

- En USA, tracking IoT pasó de "innovador" (2018) a "estándar" (2024).
- Argentina está ~5 años atrás operacionalmente — pero tiene H+Trace ya operando como semilla.
- La ventaja competitiva no es construir los sensores (commodity) sino la **plataforma de orquestación** que integra todos los stakeholders.
