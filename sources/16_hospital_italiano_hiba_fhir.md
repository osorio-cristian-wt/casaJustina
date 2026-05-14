# Hospital Italiano de Buenos Aires (HIBA) — referente FHIR Argentina

- **URLs:**
  - https://www1.hospitalitaliano.org.ar/landing/innova-salud-digital/articulos/interoperabilidad-en-salud
  - https://www.hospitalitaliano.org.ar/multimedia/archivos/servicios_attachs/1536.pdf
- **Fecha de consulta:** 2026-05-14

## Resumen

HIBA es el primer hospital de Argentina (y segundo de Latinoamérica) certificado **HIMSS EMRAM nivel 7** (100% computarizado). Lleva 25+ años en interoperabilidad sanitaria. Es **el referente regional** en HL7 FHIR + SNOMED CT.

## Información clave

### Hitos
- **1998:** Inicio del Sistema de Información en Salud (SIS).
- **2014:** Adopción HL7 FHIR.
- **Certificación HIMSS EMRAM nivel 7** (máximo nivel).
- Servidor terminológico SNOMED CT operativo.

### Stack
- HL7 v2.x, HL7 CDA, HL7 FHIR R4.
- SNOMED CT (servidor propio).
- Vacunas: primer caso productivo de intercambio FHIR.
- IPS-Argentina (International Patient Summary) — participación activa.

### Aporte al ecosistema
- HIBA capacita en informática biomédica.
- HIBA participa en grupos de trabajo del Ministerio de Salud para definición de IGs (Implementation Guides).
- HIBA participa en Conectatons.

## Relevancia para el sistema de trasplantes

HIBA es **centro de trasplante habilitado** (uno de los principales del país, junto a Favaloro, Italiano de Córdoba, Garrahan, etc.) Y al mismo tiempo es el hospital con mayor madurez digital.

Es el **partner natural** para pilotear integraciones FHIR entre HIS hospitalario y SINTRA.

## Implicancias tecnológicas

### Modelo de prueba
Un piloto técnico viable sería:
1. HIBA emite eventos FHIR cuando un paciente entra a UTI con Glasgow ≤7.
2. Un servicio intermedio recibe → notifica al coordinador hospitalario + al CUCAI provincial.
3. El operativo, si avanza, registra eventos FHIR (Encounter, Procedure, Observation, Specimen).
4. Estos eventos alimentan SINTRA vía adaptador.

### Beneficio
- Coordinador hospitalario deja de cargar manualmente datos repetidos en SINTRA.
- Tiempo real de detección a alerta CUCAI: minutos en lugar de horas.
- Trazabilidad bidireccional cumplida (Art. 38 Ley 27.447).

## Citas relevantes

> "El Hospital Italiano de Buenos Aires se convirtió en el primer hospital de Argentina y el segundo de Latinoamérica en alcanzar el nivel 7 de informatización." (HIMSS)

> "El sistema utiliza la familia HL7 y hoy se enfoca en FHIR, descripto como un gran modelo de recursos para interoperabilidad de información en salud." (HIBA)

## Observaciones

- Cualquier proyecto de modernización del ecosistema argentino de trasplantes debería tener a HIBA como early partner.
- Otros hospitales avanzados: Italiano de Córdoba, Austral, Favaloro, Garrahan.
- El gap real está en hospitales públicos provinciales — muchos sin HIS integrado o con HIS legacy sin FHIR.
- La **Provincia de Buenos Aires** está implementando HSI (Historia de Salud Integrada) que sí usa FHIR — esto eventualmente cubrirá hospitales públicos PBA.
