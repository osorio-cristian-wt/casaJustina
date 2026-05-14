# Convenio INCUCAI – Aerolíneas Argentinas (2022)

- **URL:** https://www.argentina.gob.ar/noticias/autoridades-del-ministerio-de-salud-aerolineas-argentinas-e-incucai-firmaron-convenio-0
- **Fecha del convenio:** 31-05-2022
- **Fecha de consulta:** 2026-05-14

## Resumen

Convenio entre Ministerio de Salud + Aerolíneas Argentinas + INCUCAI para optimizar traslado de órganos, tejidos, células, pacientes y profesionales del sistema público de trasplante. Aerolíneas hace ~4 envíos diarios de órganos coordinados por INCUCAI.

## Información clave

### Compromisos de Aerolíneas Argentinas
- **Transporte aéreo gratuito** de material biológico (órganos, tejidos, células).
- **Prioridad de vuelo** ante notificación INCUCAI.
- Emisión expedita de boletos y autorizaciones.
- Pasajes gratuitos para pacientes sin recursos / profesionales de salud pública.

### Operativa
- **~4 envíos diarios** de órganos por Aerolíneas.
- Cuando un vuelo es declarado sanitario: **prioridad en ruta y en aterrizaje** desde que el órgano ingresa a cabina.
- Coordinación: INCUCAI notifica → Aerolíneas asigna vuelo / lo prioriza.

## Relevancia para el sistema de trasplantes

Aerolíneas Argentinas es **la columna vertebral del transporte aéreo de órganos**. Para distancias largas no hay alternativa de jet dedicado. La dependencia es operacional y total: si un vuelo está saturado, no hay backup automático.

## Implicancias tecnológicas

### Lo que es manual hoy
- INCUCAI notifica vía telefónica/correo a Aerolíneas — no hay API documentada.
- Aerolíneas debe identificar qué vuelo asignar humanamente.
- No hay tracking en tiempo real visible para todos los actores.

### Lo que podría existir
- **API de slots de carga sanitaria** en aerolíneas.
- **Sistema de tracking GPS + temperatura** del contenedor accesible por todos los actores (procurador, INCUCAI, aerolínea, centro receptor).
- **Backup automático** si vuelo asignado falla (mecanismo de re-routing).
- **Notificación push** al centro de trasplante con ETA estimada actualizada.
- **Slot reservation system** análogo a las APIs de Cargo Booking (IATA ONE Record).

## Citas relevantes

> "Aerolíneas hace 4 envíos diarios de órganos que deben ser trasladados por el INCUCAI."

> "Cuando un vuelo se convierte en sanitario, cuenta con prioridad en ruta y en aterrizaje desde el momento en que los órganos se depositan en la cabina del avión."

## Observaciones

- La frecuencia (~4/día) sugiere que el transporte aéreo es habitual, no excepcional.
- No hay redundancia: si Aerolíneas tiene un paro, una huelga o problemas climáticos, **no hay segunda opción nacional**.
- El convenio es general — falta resolución técnica que defina procedimientos de detalle.
- No hay convenio análogo con aerolíneas privadas (Flybondi, JetSmart).
