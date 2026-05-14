# INCUCAI — Sistema Nacional de Procuración (argentina.gob.ar)

- **URL:** https://www.argentina.gob.ar/salud/incucai/comunidad-hospitalaria/sistema-nacional-procuracion
- **Fecha de consulta:** 2026-05-14
- **Tipo:** Documentación institucional oficial

## Resumen del contenido

El portal oficial describe la composición del Sistema Nacional de Procuración, los actores que intervienen, los requisitos de habilitación de centros, y las funciones de bancos de tejidos y laboratorios de histocompatibilidad.

## Información clave

### Actores del sistema

- **INCUCAI** — autoridad nacional reguladora.
- **24 Organismos Provinciales de Ablación e Implante** (CUCAI/COTAI/etc.) — integrados a las estructuras de salud pública provincial.
- **Establecimientos de salud públicos y privados** — procuran órganos y tejidos.
- **Centros de Trasplante** — habilitados según resoluciones específicas, con director y subdirector responsables; **renovación cada 2 años** mediante inspección.
- **Bancos de Tejidos** — entidades sin fines de lucro que manipulan, procesan, preservan, distribuyen y transportan tejidos.
- **Laboratorios de Histocompatibilidad (HLA)** — realizan tipificación HLA, crossmatches linfocitarios, detección y monitoreo de anticuerpos anti-HLA, y mantenimiento de seroteca.

### Sistema de información (CRESI)

CRESI es el registro nacional de instituciones, equipos de trasplante y bancos de tejidos para consulta y verificación de entidades autorizadas. Es separado de SINTRA pero conectado (`cresi.incucai.gov.ar/IniciarCresiFromSintra.do`).

## Relevancia para el sistema de trasplantes

Define el **mapa de actores y nodos** del sistema. Es la base para entender el grafo de comunicación: cada órgano viaja desde un establecimiento procurador, pasa por al menos un CUCAI provincial, es asignado por INCUCAI, y termina en un centro de trasplante habilitado.

## Implicancias tecnológicas

- La habilitación bienal de centros es un proceso burocrático que podría digitalizarse con un sistema de cumplimiento automático (auditoría continua + alertas de vencimiento).
- La separación CRESI / SINTRA sugiere **silos de datos** que requieren reconciliación manual.
- No se mencionan APIs públicas ni mecanismos de interoperabilidad entre los 24 organismos provinciales y los HIS hospitalarios.

## Citas relevantes

> "El Sistema de Procuración en Argentina está compuesto por múltiples instituciones que trabajan articuladamente para dar respuesta a los miles de pacientes que necesitan un trasplante."

## Observaciones

- No hay referencia a estándares como HL7, FHIR, OpenEHR.
- La habilitación de centros depende de inspección física, no de auditoría automatizada de datos.
- Los CUCAI provinciales son organismos jurisdiccionales con autonomía operativa — implica heterogeneidad de procesos.
