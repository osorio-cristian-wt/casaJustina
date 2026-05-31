# Evidencia: notificación automática de muerte encefálica inminente (Zier et al., AJT 2017)

- **URL:** https://www.amjtransplant.org/article/S1600-6135(22)25102-8/fulltext
- **URL secundaria:** https://www.sciencedirect.com/science/article/pii/S1600613522251028
- **URL secundaria:** https://pubmed.ncbi.nlm.nih.gov/28397363/
- **URL secundaria:** https://link.springer.com/article/10.1007/s00415-023-11938-1 (screening digital de riesgo de muerte encefálica)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** académico (American Journal of Transplantation, 2017 — Zier et al.)

## Resumen del contenido

Estudio que implementó un **sistema electrónico de soporte a la decisión (CDS)** que **notifica automáticamente al organismo de procuración (OPO)** cuando un paciente cumple los criterios de **muerte encefálica inminente**. Es la **mejor evidencia cuantitativa** de que automatizar el aviso de la etapa 2 **acelera dramáticamente la notificación y aumenta los donantes** — exactamente lo que pide el equipo.

## Información clave

### Resultados (pre-CDS vs. post-CDS)

| Métrica | Pre-CDS | Post-CDS | Cambio |
|---|---|---|---|
| **Tiempo medio a la notificación del OPO** | **30,2 horas** | **1,7 horas** | **−95 %** (de >1 día a <2 h) |
| **Notificación dentro de 1 hora de cumplir criterios** | 36 % | 70 % | casi ×2 |
| **Conversión a donante** | 50 % | 90 % | (no alcanzó significancia estadística por N chico) |
| **Donantes / muertes** | 7 de 57 | 11 de 24 | más donantes post-CDS |

- **Población:** pediátrica (niños que cumplían triggers de muerte encefálica inminente).
- **Características del sistema:** "fully automated CDS system", con **pocos falsos positivos**, **bajo costo** y **mínima interrupción del flujo de trabajo** — el contrapunto positivo al riesgo de alert fatigue ([25](25_alert_fatigue_cds_uti.md)).
- Estudio relacionado (Springer 2023): herramienta digital automatizada para **identificar pacientes de alto riesgo de muerte encefálica** con buena exactitud diagnóstica (screening automatizado).

## Relevancia para el sistema de trasplantes

Es el **caso de prueba directo de la etapa 2**: automatizar la notificación de muerte encefálica (inminente o declarada) llevó el tiempo de aviso de **30 horas a menos de 2** y duplicó tendencialmente los donantes. Para Argentina —donde la notificación hoy es manual, por teléfono/WhatsApp en cascada (cuello #3, [14](14_cuellos_botella_operacionales.md))— el margen de mejora es enorme. Y lo logra **sin saturar** al equipo (pocos falsos positivos, mínima interrupción), respondiendo a la principal objeción.

## Implicancias tecnológicas / de diseño

- **La métrica estrella del piloto es el "tiempo a la notificación":** medible, comparable (objetivo ≤1 h, como CMS — [29](29_required_referral_cms_42cfr48245.md)) y con baseline claro (hoy en horas).
- **"Fully automated + few false positives + minimal workflow interruption"** = el diseño objetivo. Refuerza la decisión de [analisis/05](../analisis/05_alertas_glasgow_his_hospitalarios.md): CDS pasivo, destinatario correcto, deduplicado.
- **Dos disparadores complementarios:** muerte encefálica **inminente** (continúa la lógica de detección PSG<7, etapa 1) y muerte encefálica **declarada/certificada** (etapa 2, [27](27_resolucion_716_2019_muerte_encefalica.md)). El mismo sistema cubre ambos.
- **Bajo costo declarado** → coherente con el costeo software-first de [analisis/05](../analisis/05_alertas_glasgow_his_hospitalarios.md).

## Citas relevantes

> "Mean time to OPO notification decreased from 30.2 hours pre-CDS to 1.7 hours post-CDS." (Zier et al., AJT 2017)

> "Notification within 1 hour of meeting criteria increased from 36% pre-CDS to 70% post-CDS." (ídem)

> "Positive outcomes were achieved with the use of a fully automated CDS system while simultaneously realizing few false-positive notifications, low costs, and minimal workflow interruption." (ídem)

## Observaciones

- **N pequeño y población pediátrica** → el aumento de conversión (50 %→90 %) **no fue estadísticamente significativo**; reportar la mejora de **tiempo** (robusta) como hallazgo principal y la de donantes como tendencia.
- Complementa la evidencia de detección pre-mortem ([21](21_automatizacion_referral_ehr_estudio.md)): juntas cubren las dos etapas del proyecto.
- Antecedente clave para el costeo y las métricas de [analisis/06](../analisis/06_disparo_logistica_muerte_encefalica.md).
