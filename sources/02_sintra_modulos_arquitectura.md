# SINTRA — Arquitectura, módulos e infraestructura técnica

- **URL:** https://sintra.incucai.gov.ar/intro/
- **Fecha de consulta:** 2026-05-14
- **Tipo:** Documentación técnica institucional

## Resumen del contenido

SINTRA (Sistema Nacional de Información de Procuración y Trasplante de la República Argentina) es el sistema informático que administra, gestiona, fiscaliza y consulta la actividad de procuración y trasplante. Iniciado a fines de 2002. Es operativo 24/7/365.

## Información clave

### Arquitectura

- **Stack:** Linux + Oracle + J2EE (Java EE).
- **Modelo:** Web-based, sin instalación local. Acceso por navegador con autenticación individual.
- **Hosting:** Hardware y software dedicados en facility propio de INCUCAI.
- **Seguridad:** Sesiones cifradas extremo a extremo. Sin más detalles públicos.

### Los 6 módulos

1. **Registro Nacional de Insuficiencia Renal Crónica Terminal (IRCT)** — pacientes en hemodiálisis/diálisis peritoneal.
2. **Listas de Espera** — receptores potenciales por órgano/tejido.
3. **Registro Nacional de Procuración** — operativos de procuración (detección, evaluación, ablación).
4. **Registro Nacional de Trasplante** — implantes, equipos intervinientes, evolución.
5. **Registro Nacional de Donantes de Órganos y Tejidos** — manifestaciones de voluntad (positivas/negativas), donantes efectivos.
6. **Registro Nacional de Donantes de Células Progenitoras Hematopoyéticas (CPH)** — conectado a **registro internacional** (única integración externa documentada).

### Base de datos

> "Centralizar la información en un único banco de datos uniforme, consistente e integrado"

Base de datos única central — modelo "single source of truth" con acceso descentralizado por roles.

### Roles de usuario

- Autoridades provinciales (CUCAI)
- Establecimientos de salud
- Financiadores (obras sociales / prepagas)
- Organizaciones de pacientes
- Público general (acceso limitado a su propia información)

## Relevancia para el sistema de trasplantes

SINTRA es **el** sistema central del ecosistema argentino de trasplantes. Toda actividad de procuración, asignación y trasplante se registra en él. Es a la vez:

- Base de datos transaccional (registros)
- Sistema de matching/asignación
- Plataforma de auditoría
- Sistema de comunicación con actores

## Implicancias tecnológicas

### Limitaciones inferidas

- **Stack antiguo (Oracle/J2EE, 2002–2003)**: probablemente monolito JSP/Struts, sin APIs REST modernas, sin eventos en tiempo real (no WebSockets, no Server-Sent Events, no Kafka).
- **Sin APIs públicas documentadas**: no hay interoperabilidad con HIS hospitalarios. Todo se carga **manualmente** vía web.
- **Sin integración FHIR/HL7**: la transferencia de datos clínicos (HLA, serologías, evolución) se rehace en cada sistema.
- **Único punto de integración externa**: registro internacional CPH (BMDW/WMDA).

### Oportunidades

- Construir capa API moderna sobre SINTRA (sin reemplazar core) con FHIR R4.
- Event bus para notificaciones en tiempo real (operativos abiertos, ofertas de órganos).
- Mobile-first para coordinadores hospitalarios (hoy entran por navegador).

## Citas relevantes

> "Sistema diseñado para operar las 24 horas, los 365 días del año, con los recaudos de seguridad necesarios para resguardar el acceso, protección y confidencialidad de la información."

> "Conformado por 6 módulos independientes de acceso y una central de reportes y estadísticas. Cada módulo cuenta con un grupo de usuarios propio y una funcionalidad particular, pero todos se encuentran interrelacionados y vinculados internamente a un único banco de datos uniforme, consistente e integrado."

## Observaciones

- El proyecto comenzó en 2003 y "su desarrollo continúa" — sugiere mantenimiento incremental sobre stack legacy.
- No se observa mención a contenedores, microservicios, ni nube — probable on-premise tradicional.
- El módulo 6 (CPH) es **el único** con integración internacional explícita.
- La existencia de un único banco de datos centralizado es a la vez fortaleza (consistencia) y cuello de botella (escalabilidad, disponibilidad).
