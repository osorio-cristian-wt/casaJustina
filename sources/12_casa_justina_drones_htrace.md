# Casa Justina + H+Trace — Drones para transporte de órganos

- **URLs:**
  - https://www.somospymes.com.ar/tecnologia/como-casa-justina-impulsa-la-innovacion-la-donacion-y-el-trasplante-argentina-n5401599
  - https://www.lanacion.com.ar/tecnologia/como-son-las-primeras-pruebas-para-el-traslado-de-organos-con-drones-para-mejorar-los-trasplantes-en-nid16052023/
  - https://mundogeo.com/en/2025/04/08/argentina-takes-off-drone-transport-is-now-a-reality/
- **Fecha de consulta:** 2026-05-14

## Resumen

Casa Justina (ONG fundada por Ezequiel Lo Cane, padre de Justina) y H+Trace (startup de logística de muestras biológicas con drones, fundada 2019) tienen un proyecto activo de transporte de órganos con drones en Argentina. ANAC autorizó las operaciones. Las pruebas iniciales se hicieron en Mar del Plata y se proyectan en 7 provincias.

## Información clave

### Actores
- **Casa Justina** — ONG, Ezequiel Lo Cane.
- **H+Trace** — startup, founders: **Emiliano Buitrago** (ex asesor técnico INCUCAI hasta 2021), Javier Cuello, Iván Fardjoume.
- **ANAC** (Administración Nacional de Aviación Civil) — autorizó operaciones.

### Especificaciones técnicas
- **Alcance:** >15 km controlados remotamente.
- **Carga útil:** >2,5 kg.
- **Contenedor:** ABS resistente, tamaño menor que una caja de zapatos.
- **Sensores en tiempo real:** shock, temperatura, humedad.

### Comparativo de tiempos
| Ruta | Ambulancia / taxi | Dron |
|---|---|---|
| Aeroparque → Hospital El Cruce (32 km) | 1 a 2,5 horas | < 30 minutos |

### Costo
- Drones **~8 veces más baratos** que ambulancia/helicóptero.

### Estado actual
- Pruebas en 3 regiones (Salta — Metán, Buenos Aires, otra no especificada).
- Plan: 7 provincias adicionales.
- Comenzó con muestras clínicas (Mar del Plata, junio 2021) y sueros antiveneno al norte.
- Casa Justina opera 7 casas físicas planeadas en provincias top de donación: Mendoza, Tucumán, Salta, Santa Fe, Córdoba, Buenos Aires, CABA.

## Relevancia para el sistema de trasplantes

**Es el caso de innovación más concreto y aplicable** de Argentina:
- Resuelve un cuello de botella real (última milla aeroparque→hospital, tráfico urbano).
- Reduce CIT (cold ischemia time) significativamente.
- Es **8x más económico** que helicóptero.
- Ya tiene autorización regulatoria (ANAC).
- Tiene patrocinio institucional (Casa Justina).

## Implicancias tecnológicas

### Stack necesario
- **Telemetría dron**: GPS + temperatura + shock + humedad → telemetría en tiempo real.
- **Sistema de routing**: planificación de rutas BVLOS (Beyond Visual Line of Sight).
- **Integración con SINTRA**: idealmente el operativo asociado a una orden de transporte trazable.
- **Sistema de control de tráfico aéreo de drones** (UTM — Unmanned Traffic Management).

### Gaps actuales
- Reglamentación BVLOS en Argentina aún limitada.
- Falta integración con vuelos comerciales (Aerolíneas Argentinas).
- Aún en fase piloto, no operacional en producción 24/7.
- No hay integración pública con SINTRA documentada.

### Oportunidad clara
- **El proyecto Casa Justina necesita una plataforma tecnológica que orqueste drones + ambulancias + vuelos comerciales + helicópteros en un solo sistema de routing dinámico.**
- Esa plataforma podría integrarse con SINTRA via APIs y emitir certificados de trazabilidad legales (Ley 27.447 Art. 38).

## Citas relevantes

> "Un viaje en taxi o ambulancia desde Aeroparque al Hospital El Cruce (32 kilómetros) puede tomar entre una y dos horas y media dependiendo del tráfico, mientras que un dron lo completa en menos de media hora."

> "El traslado en dron cuesta aproximadamente 8 veces menos que las alternativas de ambulancia o helicóptero."

## Observaciones

- Buitrago, founder de H+Trace, fue asesor técnico INCUCAI — **conexión institucional fuerte**.
- Argentina es uno de los pocos países del mundo con autorización regulatoria activa para drones en trasplantes (MissionGO en USA es el otro caso prominente).
- Casa Justina es la **organización catalizadora** de innovación en este espacio.
- **Este proyecto es el contexto natural de cualquier trabajo tecnológico en el ecosistema argentino de trasplantes.**
