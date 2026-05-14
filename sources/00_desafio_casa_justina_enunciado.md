# Desafío Casa Justina — Enunciado oficial AFIDEER 2026

- **Fuente:** Documento oficial entregado por AFIDEER / Casa Justina en el marco del Desafío de Innovación Entre Ríos 2026.
- **Fecha de recepción:** 2026-05-14
- **Tipo:** Documento canónico — define el alcance del desafío

---

## Título

> **Detección y notificación de potenciales donantes**

## Contexto

El sistema de trasplantes depende en gran medida de la identificación oportuna de potenciales donantes en terapias intensivas.

Si bien la normativa vigente establece la obligatoriedad de reportar fallecimientos en estos contextos, en la práctica este proceso no se realiza en todos los casos, y a veces tampoco se realiza en forma sistemática, lo que genera **pérdida de oportunidades de donación**.

Estas limitaciones pueden estar asociadas a factores de:
- **Conocimiento de la Ley Justina** (Ley 27.447).
- **Operativos / organizacionales** del equipo terapista.
- **Protocolos propios de la institución** desactualizados.
- **Falta de recursos adecuados**.
- **Prioridades** del equipo de terapia intensiva.

Todo lo anterior nace de **la ignorancia de la Ley Justina o de la falta de recursos adecuados**.

## Problemática

> La detección y notificación de potenciales donantes en terapias intensivas no se realiza de forma sistemática ni integrada, lo que limita la cantidad de trasplantes posibles y reduce las oportunidades de salvar vidas.

## Pregunta del desafío

> **¿Cómo podríamos mejorar la identificación y notificación de potenciales donantes en terapias intensivas, de manera que el sistema de trasplantes reciba esta información de forma oportuna y confiable?**

## Actores involucrados

- Equipos de terapia intensiva.
- Personal de salud.
- Sistema de trasplantes.
- Instituciones de salud.
- Organismos regulatorios.

## Impacto esperado

- Aumento en la cantidad de donantes detectados.
- Mejora en la eficiencia del sistema de trasplantes.
- Incremento en la cantidad de trasplantes realizados.
- Mayor aprovechamiento de oportunidades existentes.
- Mejor calidad del órgano donado → mejor calidad de vida del trasplantado.

## Restricciones

- Protocolos médicos y regulatorios propios de cada institución que **no han sido actualizados** de acuerdo a la Ley Justina.
- Carga operativa del personal de salud.
- Necesidad de **integración con sistemas existentes**.
- Sensibilidad del contexto clínico.

---

## Lectura crítica del enunciado

### Qué dice claramente
- El gap medido es **operacional + cultural**, no puramente técnico.
- Solución debe **integrar** con lo existente (no reemplazar).
- Debe **respetar la carga operativa** (no agregar burocracia).
- Debe **respetar la sensibilidad clínica** (no ser invasiva).

### Qué deja abierto (espacio de soluciones)

| Dimensión | Posible solución |
|---|---|
| **Conocimiento de la ley** | Capacitación digital, microlearning, simulación VR, gamificación |
| **Protocolos institucionales desactualizados** | Templates auditables + integración a SINTRA + alertas de cumplimiento |
| **Falta de detección oportuna** | Alertas automáticas Glasgow ≤7 desde HIS; integración con monitores UTI |
| **Falta de notificación** | Push notifications a coordinador hospitalario + CUCAI provincial + INCUCAI |
| **Identificación de pacientes NN** | RENAPER SID — análisis en `analisis/01-03` |
| **Reporte de fallecimientos en hospital** | Hospital Donante Program automatizado + scoring institucional |
| **Carga operativa** | Workflows mobile-first; un solo touchpoint, no múltiples sistemas |

### Lecciones interpretativas
- El enunciado **NO obliga** a una solución tecnológica única → admite **suite de productos** o un **producto integrado**.
- La frase "**oportuna y confiable**" implica métricas medibles (latencia, tasa de detección, tasa de falsos positivos).
- "**Integración con sistemas existentes**" cita explícita → SINTRA, HIS hospitalarios, RENAPER si aplica.
- "**Pérdida de oportunidades de donación**" es el outcome medible final → toda solución debe poder atribuirse a operativos efectivos adicionales.

---

## Categorías de solución compatibles con el enunciado

```
                  ¿Cómo mejorar detección + notificación?
                                  │
        ┌─────────────────────────┼──────────────────────────┐
        │                         │                          │
   1. DETECTAR             2. IDENTIFICAR              3. NOTIFICAR
        │                         │                          │
   ┌────┴────┐              ┌─────┴─────┐              ┌─────┴─────┐
   │         │              │           │              │           │
Alertas    HIS    Auto-      RENAPER    Wristband     Push        API
auto       hooks  detección  SID        + biometría   to CUCAI    SINTRA
Glasgow                                                push to    Tiempo
≤7                                                     INCUCAI    real
```

### Mapa hipotético de la solución integrada del proyecto

| Componente | Aborda | Estado en este repo |
|---|---|---|
| **Alertas Glasgow ≤7 desde HIS** | Detección | Documentado pero NO analizado en profundidad |
| **Identificación de NN con RENAPER** | Identificación | **Analizado en profundidad** ([analisis/01-03](../analisis/)) |
| **Plataforma de notificación realtime** | Notificación | Mencionado en `research.md` § 9 |
| **Dashboard de cumplimiento institucional** | Protocolo + auditoría | Mencionado, no analizado |
| **Microlearning Ley Justina** | Conocimiento | No analizado |
| **API FHIR sobre SINTRA** | Integración | Mencionado, requiere convenio |

---

## Implicancias para el roadmap del proyecto

El análisis de RENAPER (analisis/01-03) cubre **una de las cuatro dimensiones del enunciado** (Identificación). Para presentar una solución completa al desafío hay que abordar también:

1. **Detección automatizada** desde HIS hospitalarios (alertas Glasgow ≤7).
2. **Notificación realtime** al coordinador + CUCAI + INCUCAI.
3. **Cumplimiento institucional** (dashboard de adherencia a Ley Justina por hospital).
4. **Capacitación / cultura** del personal de UTI.

Áreas pendientes de investigar (ver `WORKFLOW.md` para metodología):
- Catálogo de HIS hospitalarios argentinos y sus capacidades de webhook/integración.
- Plataformas de notificación realtime (Twilio, FCM, Resend) con compliance.
- Indicadores de cumplimiento del Hospital Donante Program de INCUCAI.
- Programas existentes de capacitación INCUCAI / SAT / SATI.

---

## Citas relevantes

> "La detección y notificación de potenciales donantes en terapias intensivas no se realiza de forma sistemática ni integrada, lo que limita la cantidad de trasplantes posibles y reduce las oportunidades de salvar vidas."

> "Todo lo anterior nace de la ignorancia de la Ley Justina, o por falta de recursos adecuados."

## Observaciones

- El enunciado es **deliberadamente amplio** → invita a soluciones integrales o foco en una sub-dimensión bien argumentada.
- La frase "**ignorancia de la Ley Justina**" sugiere que el sponsor (Casa Justina) percibe un **componente educativo/cultural** importante.
- "**Sistemática ni integrada**" → dos atributos distintos: "sistemática" = repetible/auditada; "integrada" = conectada con SINTRA y los HIS.
