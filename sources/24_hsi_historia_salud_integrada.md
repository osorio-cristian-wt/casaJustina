# HSI — Historia de Salud Integrada (el HIS público con mayor llegada en Argentina)

- **URL:** https://www.argentina.gob.ar/salud/digital/hsi
- **URL secundaria:** https://saludenlinea.press/2025/05/12/cosapro-2025-645-efectores-y-66-municipios-implementan-la-historia-de-salud-integrada/
- **URL secundaria:** https://saludenlinea.press/2025/12/18/la-historia-de-salud-integrada-el-proyecto-argentino-que-se-consolida-como-la-mayor-red-publica-de-datos-clinicos/
- **URL secundaria:** https://exa.unicen.edu.ar/historia-de-salud-integrada/
- **Fecha de consulta:** 2026-05-31
- **Tipo:** oficial / prensa institucional

## Resumen del contenido

HSI es la **historia clínica electrónica pública** desarrollada por el Ministerio de Salud de la Nación junto a **Lamansys (spin-off del Instituto PLADEMA, UNICEN)**. Es **open source**, usa **FHIR + SNOMED CT + identificación RENAPER**, y se ha convertido en la **mayor red pública de datos clínicos de Argentina**. Es el candidato más realista para un piloto de detección PSG<7 en el sector público (vs. HIBA, que es el candidato del sector privado/alta complejidad).

## Información clave

### Adopción (2025)
- **+1.800 instituciones** conectadas a nivel nacional.
- **6 millones** de pacientes nominalizados.
- **35 millones** de documentos clínicos.
- **82.000** profesionales de salud usándola.
- En **PBA**: **645 efectores + 66 municipios** implementan HSI (2025), vs. **51 efectores en 2021** (crecimiento 12×). De los efectores PBA: 86 provinciales, 515 municipales, 23 de servicio penitenciario, 1 SAMIC.
- **Meta declarada:** para 2027, todos los efectores de PBA usando HSI.

### Arquitectura y estándares
- **Open source** ("código abierto … software basado en la colaboración abierta").
- **Estándares:** FHIR (HL7), **SNOMED CT** edición Argentina, **RENAPER** para identificación unívoca.
- **Historia clínica orientada a problemas.**
- Parte de la **Red Nacional de Interoperabilidad / "Nube Sanitaria"**; la información se distribuye entre puntos de atención (no un repositorio único central).
- Se está completando la auditoría para **compartir el recurso IPS a través del bus de interoperabilidad nacional**.
- Origen: 2020 (digitalización durante la pandemia).

## Relevancia para el sistema de trasplantes

**Alta y estratégica.** Si el adaptador de detección PSG<7 se integra con HSI **una vez**, queda potencialmente disponible para **>1.800 efectores** sin reintegrar hospital por hospital — exactamente lo contrario al problema de "código a medida por institución" del estudio de Texas ([21_automatizacion_referral_ehr_estudio.md](21_automatizacion_referral_ehr_estudio.md)). Además, al ser **open source**, el equipo puede inspeccionar cómo HSI modela el Glasgow y los datos de UTI, e incluso proponer la integración como contribución al proyecto HSI.

## Implicancias tecnológicas

- **Integración 1-a-muchos:** un solo adaptador HSI ↔ detección-donante escala a toda la red HSI.
- **Open source = inspeccionable:** se puede verificar en el código si HSI guarda el Glasgow como `Observation` estructurado y si soporta `Subscription`/webhooks o sólo API REST de lectura.
- **Camino institucional claro:** Ministerio de Salud Nacional + Lamansys/PLADEMA (UNICEN) son los interlocutores; UNICEN es ámbito universitario, afín a un proyecto AFIDEER.
- **Cobertura del sector público provincial**, justo donde el research.md marca el mayor gap de detección (hospitales públicos sin HIS integrado).

## Citas relevantes

> "Más de 1.800 instituciones conectadas, 6 millones de pacientes nominalizados, 35 millones de documentos clínicos y 82.000 profesionales de la salud son parte del sistema HSI." (Salud en Línea, 2025)

> "645 efectores y 66 municipios implementan la Historia de Salud Integrada." (COSAPRO 2025)

> "Utilización de un código abierto, es decir, un software basado en la colaboración abierta." (argentina.gob.ar/salud/digital/hsi)

## Observaciones

- **A verificar (crítico para el piloto):** versión FHIR de HSI (R4/R4B), si tiene `Subscription`/`SubscriptionTopic` o sólo API de lectura, y cómo modela el Glasgow (estructurado vs texto libre). Define push vs polling ([22_fhir_subscription_codificacion_glasgow.md](22_fhir_subscription_codificacion_glasgow.md)).
- **A verificar:** si HSI se usa efectivamente en **UTIs** (muchas instituciones HSI son CAPS/centros de primer nivel, donde no hay donantes neurocríticos). El piloto necesita un efector HSI **con UTI**.
- Complementa a HIBA como candidato privado/alta complejidad: [16_hospital_italiano_hiba_fhir.md](16_hospital_italiano_hiba_fhir.md).
- Encaja en la Red Nacional de Salud Digital ya documentada: [08_interoperabilidad_fhir_argentina.md](08_interoperabilidad_fhir_argentina.md).
