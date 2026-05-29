# Análisis — Modelo dinámico de viabilidad cardíaca y "tiempo equivalente" para logística de trasplantes

> Encuadre: el "4–6 h del corazón" trata la viabilidad como umbral binario. Este análisis propone reemplazarlo por un modelo de decaimiento térmico por fases que se calcula dinámicamente con telemetría de temperatura.
>
> Complemento de [sources/05_tiempos_isquemia.md](../sources/05_tiempos_isquemia.md), del cuello de botella #8 de research.md (sin tracking IoT en la cadena de frío) y de la arquitectura conceptual de research.md sec. 10.
>
> **Fecha:** 2026-05-29

---

## 0. TL;DR

- El estándar **"el corazón aguanta 4 a 6 h"** es una simplificación operacional: trata la viabilidad como un umbral binario aunque en realidad decae de forma exponencial desde el **paro circulatorio**, no desde la extracción.
- Un modelo de **decaimiento de primer orden con dependencia térmica vía Q₁₀** (Van't Hoff) reproduce los tiempos clínicos publicados (15 min WIT, 4–6 h CIT) con dos parámetros calibrables: `k_ref` y `Q₁₀`.
- Conclusión operativa principal: **los primeros minutos a temperatura cálida (WIT) son los más caros del operativo**. 15 min de isquemia caliente in situ a ~25 °C consumen ~20 % del presupuesto total de tiempo útil. Saber únicamente "tengo 4–6 h desde la cardioplegia" sub-estima el daño real.
- **Implicancia para el desafío Casa Justina:** justifica cuantitativamente el cuello de botella #8 (sin tracking IoT en cadena de frío) y abre dos productos concretos: contenedores IoT con telemetría de temperatura, y un módulo de **"tiempo equivalente"** sobre SINTRA o un orquestador externo que recalcula el margen restante en cada evento.

---

## 1. Encuadre — por qué el "4–6 h" estático es insuficiente

| Práctica actual | Lo que el modelo dinámico muestra |
|---|---|
| El reloj arranca con la extracción / clamping aórtico | Arranca con el **paro circulatorio** del donante: cada minuto previo a la cardioplegia a temperatura corporal cuenta multiplicado |
| 4–6 h es un valor fijo independiente del perfil térmico | El "tiempo útil" depende del perfil T(t) completo del operativo |
| Una rampa térmica suave en transporte (4 °C → 8 °C) es "aceptable" | Recorta el tiempo útil ~26 % — significativo en operativos largos |
| WIT y CIT se reportan como horas separadas | El daño se **encadena**: la viabilidad al final de una fase es condición inicial de la siguiente |

El modelo formaliza esa intuición con una EDO de primer orden y la valida contra los tiempos publicados en la literatura clínica (ver fuentes en §9).

---

## 2. Modelo conceptual

### 2.1 Fases reales del operativo

| Fase | Cuándo | Temperatura típica | Duración típica |
|---|---|---|---|
| 1. Isquemia caliente in situ (WIT) | Desde paro circulatorio hasta primer enfriamiento | 25–37 °C | 5–30 min |
| 2. Enfriamiento + extracción | Cardioplegia fría in situ + cirugía | 4–15 °C | 10–30 min |
| 3. Cold storage (CIT) | Transporte estático en hielo | 4 °C | 1–6 h |
| 4. Reperfusión | Anastomosis + restablecimiento de flujo | sube a 37 °C | 30–60 min |

El modelo cubre las fases 1+2+3 (todo lo que ocurre con el órgano sin perfusión efectiva). La fase 4 tiene dinámica propia (radicales libres, daño por reperfusión) y queda fuera del alcance.

### 2.2 Forma del modelo

Decaimiento exponencial de la viabilidad V(t) ∈ [0, 1] con constante de degradación dependiente de la temperatura (ley de Van't Hoff / Q₁₀):

- **V(t) = V₀ · exp(−k(T) · t)**, con V₀ = 1 al instante del paro.
- **k(T) = k_ref · Q₁₀^((T − T_ref) / 10)**, con T_ref = 37 °C.

Interpretación: por cada 10 °C que se enfría el órgano, la velocidad de degradación se divide por Q₁₀ (~2.12 calibrado). El frío no detiene el daño, lo enlentece 10–20×.

### 2.3 Calibración con literatura clínica

| Parámetro | Valor de trabajo | Origen |
|---|---|---|
| `k_ref` (a 37 °C) | 1.39 h⁻¹ | WIT cardíaca: ~50 % viabilidad en 30 min |
| `Q₁₀` | 2.12 | Despejado entre k(37) y k(4) usando CIT máximo 6 h |
| `T_ref` | 37 °C | Temperatura corporal |
| `V_min` | 0.5 | Umbral clínico práctico para trasplante exitoso |

Los k(T) resultantes son consistentes con la regla biológica Q₁₀ ∈ [2, 3] para tejidos.

### 2.4 Variables que el modelo NO captura

| Variable real | Tratamiento en el modelo |
|---|---|
| Edad del donante | Ignorada — k_ref absorbe un valor "promedio" |
| Comorbilidades (HTA, diabetes, sepsis) | Ignoradas |
| Tipo de solución de preservación (UW, HTK, Celsior) | Absorbidas en k_ref |
| pH, lactato, ATP medido | No medidos, aproximados vía V agregada |
| Daño previo (paro prolongado, RCP) | Asume V₀ = 1 |
| Heterogeneidad intra-órgano (centro vs. superficie) | Modelo de un compartimento, T uniforme |
| Daño de reperfusión | Excluido — el modelo termina antes del implante |

Decisión metodológica: capturar la **dinámica esencial** del fenómeno con tres variables (t, T, V) para uso logístico, no reproducir la bioquímica completa.

---

## 3. Resultados cuantitativos relevantes

### 3.1 Viabilidad a temperatura constante

Con `k_ref = 1.39 h⁻¹` y `Q₁₀ = 2.12`:

| T (°C) | k(T) [h⁻¹] | V(1 h) | V(4 h) | V(6 h) | Tiempo hasta V = 0.5 |
|---|---|---|---|---|---|
| 4  | 0.116 | 0.890 | 0.628 | 0.497 | **5.95 h** |
| 8  | 0.157 | 0.854 | 0.533 | 0.389 | **4.41 h** |
| 15 | 0.266 | 0.766 | 0.345 | 0.203 | **2.60 h** |
| 25 | 0.564 | 0.569 | 0.105 | 0.034 | **1.23 h** |
| 37 | 1.390 | 0.249 | 0.004 | < 0.001 | **0.50 h** |

> **Lecturas operacionales:**
> - Un salto de 4 °C → 8 °C en el contenedor (falla térmica suave) reduce el tiempo útil ~26 %.
> - Un salto a 25 °C (falla grave) lo reduce ~80 %.

### 3.2 Encadenamiento de fases — el costo real del WIT

Escenario: paro circulatorio en un accidente. El órgano queda 15 min a ~25 °C hasta cardioplegia. Después se transporta a 4 °C.

| Cálculo | Valor |
|---|---|
| V al terminar WIT (15 min @ 25 °C) | **0.868** (ya perdió ~13 %) |
| Tiempo restante a 4 °C hasta cruzar V = 0.5 | **4.74 h** |
| Tiempo útil si la cardioplegia fuera inmediata (sin WIT) | 5.95 h |
| **Costo real de esos 15 min "calientes"** | **~1.21 h menos de transporte** (~20 % del presupuesto) |

Un retraso aparentemente menor (un cuarto de hora pre-cardioplegia) consume cerca del 20 % del presupuesto total. Una decisión logística que mire sólo "tengo 6 h desde el frío" se equivoca por más de una hora.

### 3.3 Métrica unificada: "tiempo equivalente"

Como cada fase aporta un término al exponente, todo el daño se puede expresar como un **tiempo equivalente a una temperatura de referencia**. En el ejemplo anterior, los 15 min a 25 °C equivalen a ~1.21 h de cold storage a 4 °C. Esto habilita:

- Comparar perfiles logísticos heterogéneos (WIT corto + transporte largo vs. WIT largo + transporte corto) en una sola escala.
- Definir un único KPI operacional ("equivalente CIT a 4 °C") en lugar de manejar WIT + CIT por separado.
- Alimentar el algoritmo de asignación con margen restante real, no nominal.

---

## 4. Conclusiones operacionales

1. **Saber que "los primeros minutos importan" no alcanza** — el valor está en cuantificar cuánto. Un coordinador que arranca el reloj con la cardioplegia (no con el paro) sobreestima el margen entre 10 y 30 % según el WIT.
2. **El reloj operativo debe arrancar con el paro circulatorio**, no con el clamping aórtico o la cardioplegia. Esto debería declararse explícitamente en cualquier dashboard de timeline compartido.
3. **La temperatura del contenedor no es binaria** ("frío / no frío"). Una rampa térmica de 4 → 8 °C cuesta ~26 % de margen y hoy es invisible porque no hay telemetría.
4. **El "4–6 h" estándar de los protocolos debería leerse como "presupuesto a usar con telemetría"**, no como certificado de viabilidad.
5. **Una métrica unificada de "tiempo equivalente"** habilita comparaciones entre operativos y entre rutas. Candidato natural a KPI compartido entre INCUCAI, CUCAI provincial y equipos de transporte.

---

## 5. Implicancias para la arquitectura propuesta en `research.md`

Mapeo directo a la arquitectura de tres capas (sec. 10 de research.md):

| Capa | Componente | Aporte del modelo |
|---|---|---|
| Layer 1 — Integration | **IoT Telemetry (TimescaleDB / Influx)** | Sensores T(t) en contenedor + cuerpo donante alimentan el cálculo de k(T) instantáneo |
| Layer 1 — Integration | **Event Bus (Kafka / NATS)** | Cada cambio de fase (paro → cardioplegia → transporte → implante) emite evento que dispara recálculo |
| Layer 2 — Applications | **Workflow Orchestrator (Temporal)** | Mantiene el estado V(t) acumulado, reasigna o alerta cuando el margen cae bajo umbral |
| Layer 2 — Applications | **Mobile app coordinador** | Muestra "tiempo equivalente restante" en lugar de cronómetro lineal |
| Layer 2 — Applications | **ML services** | Refina k_ref y Q₁₀ con datos reales por hospital/protocolo/órgano |

Conecta también con el cuello de botella #8 (sin tracking IoT cadena frío): el modelo dinámico es **la razón por la que la telemetría tiene valor clínico**, no sólo de auditoría.

---

## 6. Limitaciones a tener presentes

- **El modelo exponencial puro subestima la zona crítica > 5–6 h.** Los datos de PGD (incidencia 2.3–28 %, severa ~10 %) muestran que cada hora adicional de CIT casi duplica el riesgo de PGD severa. La forma exponencial no captura ese codo. Mejora futura: modelo con umbral (logístico / sigmoide) o término extra de daño por reperfusión.
- **Calibración con dos puntos clínicos** (30 min @ 37 °C y 6 h @ 4 °C). Insuficiente para producción. Para piloto: pedir a INCUCAI / centros datos de operativos reales con tiempo y temperatura registrados.
- **No considera al donante.** Donantes < 20 años toleran > 6 h CIT; > 33 años, < 3.5 h ideal. Esto debe incorporarse antes de uso clínico.
- **Modelo de un compartimento.** Asume T uniforme en el órgano. Razonable para corazón (se enfría en ~10 min en solución fría); requiere validación para hígado y riñón.
- **Sólo cubre corazón.** Los k_ref y Q₁₀ cambian por órgano y por solución de preservación. Reutilizar la metodología para hígado, pulmones y riñón exige re-calibración.
- **OCS / perfusión normotérmica cambia el modelo estructuralmente** — al agregar un término +λ(V_max − V) reaparece un equilibrio con V* > 0. Si el ecosistema argentino adopta TransMedics OCS o equivalente, este modelo deja de aplicar como está.

---

## 7. Próximos pasos posibles

1. **Validación con datos reales.** Pedir a un CUCAI / centro de trasplante un set anonimizado de operativos cardíacos con tiempos y temperaturas. Refit de k_ref y Q₁₀ por mínimos cuadrados sobre log V.
2. **Extender a otros órganos.** Hígado, pulmones, riñón — cada uno con su k_ref y Q₁₀.
3. **POC dashboard "tiempo equivalente".** Webapp simple que reciba lecturas de temperatura (manuales primero, IoT después) y muestre margen restante.
4. **Especificación de contenedor IoT.** Usar este modelo para dar requerimientos cuantitativos al BOM del contenedor (frecuencia de muestreo, exactitud del sensor, tolerancia de alarma).
5. **Integración con orquestador.** Definir el contrato de eventos para que cada cambio de fase recalcule el margen y dispare alertas.

---

## 8. Ficheros relacionados

- [sources/05_tiempos_isquemia.md](../sources/05_tiempos_isquemia.md) — datos clínicos de CIT/WIT por órgano usados para calibrar.
- [research.md](../research.md) — sec. 6 (logística), sec. 8 cuello #8 (sin tracking IoT), sec. 9 oportunidad A.3 (contenedores IoT) y B.3 (routing dinámico), sec. 10 (arquitectura).
- [analisis/01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md) — encuadre del desafío AFIDEER.

---

## 9. Fuentes de literatura clínica usadas para la calibración

**Tiempos de isquemia y preservación**
- StatPearls — *Myocardial Protection*: https://www.ncbi.nlm.nih.gov/books/NBK567795/
- Frontiers Cardiovasc Med 2023 — *Graft preservation in heart transplantation*: https://www.frontiersin.org/journals/cardiovascular-medicine/articles/10.3389/fcvm.2023.1253579/full
- PMC 2022 — *Hypothermia prevents cardiac dysfunction during acute ischemia reperfusion*: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9301761/

**Q₁₀ y ley de Van't Hoff en sistemas biológicos**
- Wikipedia — *Q10 (temperature coefficient)*: https://en.wikipedia.org/wiki/Q10_(temperature_coefficient)
- Mundim et al. 2020 — *Temperature coefficient (Q10) and its applications in biological systems*, Ecological Modelling: https://www.sciencedirect.com/science/article/abs/pii/S030438002030199X
- PMC — *Impact of temperature on cross-bridge cycling kinetics in rat myocardium*: https://pmc.ncbi.nlm.nih.gov/articles/PMC2277159/

**Hipotermia y protección miocárdica**
- AHA Journals 2021 — *Physiological impact of hypothermia*: https://journals.physiology.org/doi/full/10.1152/physiol.00025.2021
- Circulation 2000 — *ATP synthesis during low-flow ischemia*: https://www.ahajournals.org/doi/10.1161/01.cir.101.17.2090

**PGD y CIT (datos clínicos)**
- American Journal of Transplantation 2022 — *Primary graft dysfunction after heart transplantation*: https://www.amjtransplant.org/article/S1600-6135(22)09577-6/fulltext
- PMC 2024 — *Risk factors for PGD: meta-analysis*: https://pmc.ncbi.nlm.nih.gov/articles/PMC13019779/

**OCS / TransMedics (perfusión normotérmica ex vivo)**
- Frontiers Cardiovasc Med 2024 — *Extending heart preservation to 24 h with normothermic perfusion*: https://www.frontiersin.org/journals/cardiovascular-medicine/articles/10.3389/fcvm.2024.1325169/full
- PMC — *Ex Vivo Heart Perfusion for cardiac transplantation: initial US experience*: https://pmc.ncbi.nlm.nih.gov/articles/PMC9949869/
