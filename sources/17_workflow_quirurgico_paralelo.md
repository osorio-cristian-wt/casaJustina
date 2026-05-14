# Coordinación de equipos quirúrgicos paralelos

- **URLs:**
  - https://www.lanacion.com.ar/salud/como-se-hace-un-trasplante-6-horas-junto-al-equipo-del-hospital-italiano-nid2235513/
  - https://www.ambito.com/informacion-general/el-hospital-garrahan-realizo-exito-un-doble-trasplante-simultaneo-pacientes-pediatricos-n6259751
  - https://www.garrahan.gov.ar/prensa/04-12-2024-txinedito
- **Fecha de consulta:** 2026-05-14

## Resumen

Un operativo de procuración-trasplante puede involucrar hasta 150 profesionales en paralelo, distribuidos geográficamente. Caso Garrahan: doble trasplante simultáneo (corazón + hígado) con 50+ profesionales en quirófanos contiguos.

## Información clave

### Escala humana del operativo
- **Hasta 150 profesionales** pueden intervenir en un operativo multi-órgano.
- **Geográficamente dispersos**: donante en provincia X, receptores en provincias diferentes.
- **Ubicaciones simultáneas**:
  - UTI del hospital donante.
  - Quirófano de ablación.
  - INCUCAI / CUCAI (coordinación central).
  - Laboratorio HLA.
  - Centros de trasplante (1 por órgano).
  - Vehículos en tránsito (ambulancias, vuelos).

### Coordinación temporal
- "Una vez aceptados los órganos, se acuerda la hora de ablación."
- Tiene que sincronizar:
  - Disponibilidad de quirófano en el centro de trasplante.
  - Disponibilidad del equipo trasplantista.
  - Receptor preparado (admisión, ayuno, preanestesia).
  - Vuelo / ruta disponible.
  - Equipo de procuración listo en el hospital donante.

### Caso Garrahan (diciembre 2024)
- Doble trasplante simultáneo pediátrico.
- Cardíaco (cardiopatía ventrículo único, 6h) + hepático (inmunodeficiencia severa).
- **Quirófanos contiguos** para "coordinación en paralelo".
- 50+ profesionales.
- Madrugada del sábado.

## Relevancia para el sistema de trasplantes

Este es **el caso de uso más complejo de orquestación operacional**. Hoy se resuelve con:
- Coordinador hospitalario llamando por teléfono / WhatsApp.
- Checklists en papel o spreadsheets.
- Reuniones síncronas (a veces virtuales).
- SINTRA como "registro" pero no como "orquestador".

## Implicancias tecnológicas

### Lo que falta
- **Timeline compartido en tiempo real** entre todos los actores.
- **Asignación visible de tareas** (quién hace qué, cuándo).
- **Estados por actor** (en camino, listo, en quirófano, etc.).
- **Bloqueos críticos** visibles (ej: receptor no llegó al hospital).
- **Alertas tempranas** si una etapa va a retrasarse y comprometer CIT.

### Modelo de software
Esto es un **caso de uso clásico de orquestación de workflow distribuido**:
- Cada actor es un "worker" en el workflow.
- Cada órgano tiene su propio "saga" con compensaciones.
- El estado se comparte en tiempo real (estilo Linear, Asana, Notion, pero domain-specific).
- Eventos sincronizados con sensores físicos (contenedor, máquina de perfusión).

Frameworks aplicables:
- Temporal.io / Cadence (workflow orchestration).
- Kafka / NATS (event streaming).
- WebSockets / SSE (UI en tiempo real).
- Mobile-first para coordinadores en el terreno.

## Citas relevantes

> "En el proceso de obtención de órganos y tejidos para trasplante llegan a intervenir hasta 150 profesionales especializados."

> "Las intervenciones, en la que participaron más de 50 profesionales, comenzaron en la madrugada del sábado y se llevaron a cabo en quirófanos contiguos, lo que permitió la coordinación en paralelo." (Garrahan)

## Observaciones

- Los operativos exitosos son testimonio de **excelencia humana**, no de excelencia tecnológica.
- Esa excelencia se mantiene con **personas heroicas**, lo que no escala.
- Si Argentina quiere pasar de 17 a 30 DPMH (Portugal level) → necesitará **tecnología de orquestación** porque las personas no escalan 2x sin burnout.
- El caso Garrahan es **excepcional** por la complejidad; el caso promedio es más simple, pero igualmente sub-orquestado tecnológicamente.
