# Captura de datos desde monitores de UTI — y por qué el Glasgow NO sale del monitor

- **URL:** https://www.usa.philips.com/healthcare/medical-specialties/acute-care-capabilities/articles/interoperability
- **URL secundaria:** https://sourceforge.net/projects/vscapture/ (VitalSignsCapture — open source)
- **URL secundaria:** https://www.medicollector.com/store/p1/patient-data-signals-recording-medicollector-bedside.html
- **URL secundaria:** https://www.biyomar.com/uploads/katalog/monitor-cihazlari/piic-ix.pdf (IntelliVue Information Center iX — datasheet HL7)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** técnico (fabricantes + software de captura)

## Resumen del contenido

Aclara una confusión frecuente y central para la arquitectura: **los monitores de signos vitales de UTI NO calculan ni exportan el Glasgow**. El Glasgow es una **valoración clínica/asistencial** (la hace y registra el personal de enfermería/médico, en general por turno) que vive en el **HIS/planilla de enfermería**, no en el monitor. Los monitores sí exportan parámetros fisiológicos (FC, SpO₂, TA, etc.), pero por una vía distinta (HL7 v2 desde la central de monitoreo). Esto define de **dónde** debe leer el adaptador.

## Información clave

### Qué exporta cada cosa

| Dato | Origen real | Cómo se exporta |
|---|---|---|
| **Glasgow Coma Score** | Valoración del **enfermero/médico** | Campo estructurado en el **HIS/HCE** (planilla de enfermería) → FHIR `Observation` LOINC 9269-2 |
| FC, SpO₂, TA, FR, Tº | Monitor de cabecera | HL7 v2 desde la **central de monitoreo** |
| Estado de ventilación | Respirador / monitor | HL7 v2 / propietario |

### Monitores y su salida de datos

- **Philips IntelliVue:** el protocolo LAN del monitor es **UDP/IP** y **no manda HL7 TCP/IP "out of the box"**. Para salida HL7 (vital signs outbound, ADT, report/wave, lab) se usa la central **IntelliVue Information Center (iX)**.
- **GE Carescape / Dash** y **Mindray BeneVision:** exponen interfaces HL7 v2 a través de sus estaciones/gateways (esquema análogo: el dato sale de la central, no del monitor individual).
- **Software de captura intermedio:**
  - **VitalSignsCapture (vscapture):** app open-source (C#/.NET) que captura datos y ondas de Philips IntelliVue, GE Dash y monitores HL7 Mindray.
  - **MediCollector BEDSIDE:** adquiere/streamea datos de monitores Philips y GE y los envía a un HIS vía **HL7**.

## Relevancia para el sistema de trasplantes

Determina **el punto de captura correcto** del proyecto. Como el trigger del INCUCAI es el **Glasgow ≤7** ([19_incucai_deteccion_psg7_garantia_calidad.md](19_incucai_deteccion_psg7_garantia_calidad.md)), y el Glasgow **no está en el monitor**, el adaptador **debe leer del HIS/HCE**, no del monitor. Una arquitectura "caja IoT que escucha el monitor" puede detectar deterioro fisiológico (señal temprana), pero **no** el Glasgow — por lo que es complemento, no reemplazo, de la lectura del HIS.

## Implicancias tecnológicas

- **Confirma la prioridad de la integración con HIS/FHIR** (fuente del Glasgow) por sobre la captura desde monitor.
- **Vía monitor como señal complementaria pre-mortem:** caída sostenida de FC/TA, desaturación o patrones de deterioro neurológico pueden anticipar el evento incluso antes de que enfermería actualice el Glasgow. Útil como "pre-alerta", no como trigger formal.
- **Bridge HL7 v2 reutilizable:** dado que tanto los datos de monitor como muchos HIS legacy hablan HL7 v2.x (`ORU^R01`), el adaptador debería incluir un parser HL7 v2 → FHIR para cubrir hospitales sin FHIR.
- **Riesgo de "HIS bypass":** leer del monitor evita depender del HIS, pero pierde el Glasgow y agrega hardware por cama → sólo tiene sentido en hospitales sin HIS digital.

## Citas relevantes

> "The IntelliVue LAN data protocol is based on UDP/IP, so the monitor is not able to send TCP/IP based HL7 messages out of the box — for this purpose, you need to use the HL7 communication interface provided by the central station (the IntelliVue Information Center)." (foro técnico EBME / datasheet PIIC iX)

> "VitalSignsCapture ... download or capture data from several medical device interfaces including Philips IntelliVue, GE Dash, Mindray HL7 patient monitors." (SourceForge)

## Observaciones

- **Implicancia de diseño fuerte:** el Glasgow es dato **asistencial cargado a mano** → su frescura depende de la frecuencia de registro de enfermería (típicamente por turno). La latencia de detección la limita la práctica de carga, no la tecnología. **A verificar** la frecuencia real de registro de Glasgow en UTIs argentinas.
- **A verificar:** si los HIS argentinos exponen el Glasgow como `Observation` estructurado o como texto libre en evolución (esto cambia radicalmente la viabilidad del filtro automático).
- Cómo se empuja el evento una vez que está en el HIS: [22_fhir_subscription_codificacion_glasgow.md](22_fhir_subscription_codificacion_glasgow.md).
