# Interoperabilidad sanitaria en Argentina — Red Nacional de Salud Digital

- **URLs:**
  - https://www.argentina.gob.ar/salud/digital/estandares
  - https://www.argentina.gob.ar/salud/digital/recursos-de-la-direccion-nacional-de-sistemas-de-informacion-sanitaria
  - https://github.com/SALUD-AR/IPS-Argentina
  - https://conectaton.msal.gob.ar/
- **Fecha de consulta:** 2026-05-14

## Resumen

Argentina ha adoptado oficialmente FHIR + SNOMED CT como estándares de interoperabilidad sanitaria a través de la **Resolución 680/2018** del Ministerio de Salud, y construye la **Red Nacional de Salud Digital** sobre estos pilares. Existe un grupo de trabajo activo IPS-Argentina (International Patient Summary) en GitHub.

## Estándares adoptados

| Estándar | Uso |
|---|---|
| **HL7 FHIR R4** | Intercambio de información clínica vía REST. |
| **SNOMED CT (edición Argentina)** | Codificación clínica (diagnósticos, procedimientos, medicamentos, dispositivos). |
| **ICD-10 / ICD-11** | Estadística epidemiológica. |
| **IHE Profiles** | Perfiles de integración. |
| **IPS (International Patient Summary)** | Resumen clínico portable. |
| **RENAPER** | Identificación unívoca de personas. |
| **REFEPS / REFES** | Registros nacionales de profesionales y establecimientos. |
| **MPI (Master Patient Index)** | Identificación de pacientes. |

## Infraestructura

- **Red Nacional de Salud Digital**: servicios web REST con FHIR.
- **HSI (Historia de Salud Integrada)**: implementación PBA, usa FHIR.
- **Guías de implementación**: openrsd.msal.gov.ar
- **Conectaton**: eventos de testing de interoperabilidad (conectaton.msal.gob.ar).
- **Servidores terminológicos** open-source provistos por Ministerio.

## Marco normativo

- **Resolución 680/2018** del Ministerio de Salud: establece estándares nacionales.
- **Estrategia Nacional de Salud Digital**: plan macro.
- **Ley 27.706** (Ley de Salud Digital, 2023): marco para historia clínica nacional digital interoperable.

## Relevancia para el sistema de trasplantes

**Crítica**. Argentina YA tiene la infraestructura normativa y técnica para interoperar datos clínicos vía FHIR — pero **SINTRA está fuera de esta red**. La oportunidad inmediata es:

1. Definir perfiles FHIR para datos de trasplante (donante, receptor, organ offer, allocation).
2. Crear un **adaptador FHIR sobre SINTRA** sin modificar el core.
3. Conectar HIS hospitalarios → SINTRA vía FHIR.

## Implicancias tecnológicas

### Lo que existe
- FHIR adoptado oficialmente.
- Servidores terminológicos open-source.
- Grupo activo SALUD-AR en GitHub.
- Conectatons para testing.

### Lo que falta
- **Recursos FHIR específicos para trasplantes** (no hay un IG argentino de Transplant aún).
- **Adopción real en hospitales**: muchas instituciones siguen con HL7 v2.x o sin estándar.
- **Integración SINTRA**: no documentada.
- **APIs públicas de INCUCAI**: no existen.

### Posible IG (Implementation Guide) Argentina-Transplant
Recursos FHIR útiles:
- `Patient` (donante / receptor)
- `Practitioner` / `PractitionerRole` (equipo procurador, equipo trasplantista)
- `Organization` (CUCAI, hospital, centro de trasplante)
- `Encounter` (operativo de procuración, implante)
- `Observation` (HLA, serologías, MELD/PELD score)
- `Condition` (causa de muerte, comorbilidades)
- `Procedure` (ablación, implante)
- `BodyStructure` (órgano procurado — extensión)
- `Specimen` (muestra para tipificación)
- `DeviceUseStatement` (machine perfusion)
- `Task` (ofertas, aceptaciones, rechazos)
- `Communication` (notificaciones)

## Observaciones

- La existencia de IPS-Argentina demuestra capacidad técnica nacional para FHIR.
- El Hospital Italiano de Buenos Aires (HIBA) es referente regional en FHIR.
- INCUCAI no figura como participante activo de los grupos de FHIR Argentina — **gap institucional**.
- Cualquier startup/proyecto en este espacio debería trabajar **con** el ecosistema FHIR-AR existente, no en paralelo.
