# TP Integrador AM III — Modelo de decaimiento de viabilidad cardíaca durante transporte

> Trabajo Práctico Integrador — Unidad N° 1: Ecuaciones Diferenciales Ordinarias
> Análisis Matemático III — Ingeniería en Sistemas — UAP
> Documento de trabajo (puntos 1 y 2; borradores para 3–7)
> **Nota:** este archivo es para un proyecto académico distinto al resto del repo. Se apoya en el contexto temático de Casa Justina (logística de trasplantes) pero **no sigue la plantilla del repositorio** (no es una fuente ni un análisis del desafío AFIDEER).

---

## 0. Contexto del proyecto (resumen para el lector externo)

El sistema argentino de trasplantes está limitado por la **ventana de isquemia fría (CIT)** de cada órgano. El corazón es el caso más exigente: **4–6 h de CIT máximo aceptado** en preservación estática en frío. Esa ventana fija el radio máximo de transporte (Buenos Aires ↔ Mendoza ya está en el límite, ver `sources/05_tiempos_isquemia.md` del repo).

La logística actual asume una ventana fija (4 h, 6 h) sin modelar **cómo varía la viabilidad efectiva con la temperatura real de transporte**. Si se pudiera **computar el decaimiento** en tiempo real (con sensores de temperatura en el contenedor), se podría:

- Estimar dinámicamente el **radio de transporte aceptable**.
- Decidir entre rutas con distinto perfil térmico/temporal.
- Alertar tempranamente cuando un operativo va a superar el umbral de viabilidad.
- Justificar técnicamente extender o acortar el horizonte respecto del “4–6 h” genérico.

Este TP construye el **modelo matemático base** (EDO de primer orden) para esa funcionalidad.

---

## 1. Selección y contextualización del problema

### 1.1 Fenómeno seleccionado

**Decaimiento de la viabilidad de un órgano (caso: corazón) desde el momento de la muerte del donante hasta el implante en el receptor**, en función del tiempo y de la temperatura a la que el órgano está expuesto en cada fase del proceso.

El reloj **no empieza con la extracción**: empieza con la parada circulatoria (por ejemplo, en un accidente de tránsito o en una muerte encefálica seguida de paro). Desde ese instante, el órgano está sin perfusión efectiva y el daño se acumula. La temperatura cambia varias veces durante el proceso (cuerpo todavía tibio → enfriamiento in situ → cardioplegia a 4 °C → transporte → implante), y **cada fase con su temperatura aporta su propio daño** al total.

### 1.2 Descripción física/biológica

Al cesar la perfusión en el cuerpo del donante, las células miocárdicas dejan de recibir oxígeno y sustratos. Independientemente de la temperatura:

1. La fosforilación oxidativa se detiene → **caída del ATP**.
2. Las bombas Na⁺/K⁺-ATPasa fallan → **edema celular** y desequilibrio iónico.
3. Se acumula lactato, baja el pH (de 7.4 a ~6.0).
4. Al reperfundir en el receptor, se desencadena **daño por isquemia-reperfusión** (radicales libres, sobrecarga de Ca²⁺, apertura del MPTP mitocondrial).

La **temperatura controla la velocidad de todos estos procesos**: enfriar a 4 °C reduce el metabolismo basal a ~3–5 % del valor a 37 °C (ley de Van’t Hoff / Q₁₀ ≈ 2–3 en tejidos biológicos). Por eso la preservación estándar es **cold static storage** con soluciones cardiopléjicas (Custodiol/HTK, Celsior, UW) a 4 °C — *no porque el frío detenga el daño, sino porque lo enlentece ~10–20 veces*.

### 1.3 Las fases del proceso (cronología real, no idealizada)

| Fase | Cuándo | Temperatura típica | Duración típica |
|---|---|---|---|
| **0. Pre-muerte** | Antes del paro | 37 °C, perfusión normal | n/a (V=1 inicial) |
| **1. Isquemia caliente in situ (WIT)** | Desde paro circulatorio hasta primer enfriamiento | 25–37 °C (cuerpo enfriándose) | 5–30 min (variable, crítico) |
| **2. Enfriamiento + extracción** | Cardioplegia fría in situ + cirugía | 4–15 °C | 10–30 min |
| **3. Cold storage (CIT)** | Transporte estático en hielo | 4 °C | 1–6 h (lo que se mide hoy) |
| **4. Re-implante y reperfusión** | Anastomosis + restablecimiento de flujo | sube a 37 °C | 30–60 min |

El TP modela **fases 1 + 2 + 3** (todo lo que ocurre con el órgano sin perfusión efectiva). La fase 4 (reperfusión) tiene dinámica propia (puede *aumentar* el daño por radicales libres) y queda fuera.

Operacionalmente esto define dos regímenes de referencia:
- **Isquemia caliente (WIT)** a ~37 °C: el corazón tolera ~15–30 min sin cardioplegia.
- **Isquemia fría (CIT)** a ~4 °C: el corazón tolera 4–6 h estándar; hasta 6–8 h en donantes jóvenes.

Por encima del umbral combinado, el riesgo de **disfunción primaria del injerto (PGD)** crece de forma marcadamente no lineal: en metaanálisis recientes, *cada hora adicional de CIT prácticamente duplica el riesgo de PGD severa* (incidencia base ~10.5 %, mortalidad 30 días asociada ~38.6 %).

### 1.4 Variables del modelo

| Símbolo | Variable | Tipo | Unidad |
|---|---|---|---|
| **t** | Tiempo desde clampeo aórtico | Independiente | horas (h) |
| **T** | Temperatura del órgano | Independiente (parámetro o función T(t)) | grados Celsius (°C) |
| **V(t)** | Viabilidad del órgano (fracción funcional respecto del estado inicial) | Dependiente | adimensional, V ∈ [0, 1] |
| **k(T)** | Constante de degradación a temperatura T | Parámetro | h⁻¹ |
| **k_ref** | Constante de degradación a temperatura de referencia | Parámetro | h⁻¹ |
| **T_ref** | Temperatura de referencia (37 °C) | Constante | °C |
| **Q₁₀** | Coeficiente de Van’t Hoff (factor por cada 10 °C) | Parámetro | adimensional |
| **V_min** | Umbral mínimo de viabilidad para trasplante exitoso | Parámetro | adimensional (~0.5–0.7 según criterio clínico) |

**Interpretación de V(t):** se define como una métrica agregada normalizada (V=1 al momento de la extracción, V=0 = inviable). En la práctica clínica se aproxima combinando ATP residual, integridad de membrana (LDH), edema, función contráctil post-reperfusión. Para el modelo teórico la tratamos como una sola variable de estado.

### 1.4 Por qué es interesante computarizar este modelo

- Permite reemplazar la regla rígida “6 horas máximo” por un cálculo dinámico **V(t, T(t))** basado en el perfil térmico real del contenedor.
- Si en algún tramo del transporte la temperatura sube de 4 a 8 °C (falla de hielo seco, demora en ambulancia, espera en pista), el modelo cuantifica cuánto **se acorta** el tiempo útil restante.
- Sirve como base para algoritmos de **routing dinámico** y **re-asignación** del operativo.

---

## 2. Modelización matemática

### 2.1 Hipótesis del modelo (versión base)

**H1.** La velocidad de pérdida de viabilidad es proporcional a la viabilidad actual (modelo de primer orden, análogo a desintegración radiactiva o ley de enfriamiento de Newton):
> *Cuanto más viable está el órgano, más sustrato hay para degradar.* Es la hipótesis más simple compatible con literatura de cinética celular.

**H2.** La constante de proporcionalidad **k(T)** depende exclusivamente de la temperatura, según la ley de Van’t Hoff (equivalente “suave” a la ecuación de Arrhenius en el rango fisiológico 0–40 °C):

$$ k(T) = k_{ref} \cdot Q_{10}^{(T - T_{ref})/10} $$

**H3.** No hay fuentes que sumen viabilidad (no hay reparación celular sin perfusión activa). En cold static storage sólo hay términos de pérdida.

**H4.** Temperatura uniforme en el órgano (modelo de un compartimento; razonable porque el corazón se enfría en ~10 min en solución fría).

**H5.** Se ignora explícitamente la fase de reperfusión (que tiene su propia dinámica). El modelo termina cuando se reperfunde en el receptor.

### 2.2 Formulación de la EDO

Aplicando H1 + H3:

$$ \boxed{\;\frac{dV}{dt} = -k(T) \cdot V\;} $$

con condición inicial **V(0) = V₀ = 1** (órgano íntegro al momento de clampear).

**Términos del modelo:**

| Término | Signo | Justificación física |
|---|---|---|
| **−k(T)·V** | resta | Degradación metabólica (ATP↓, edema, acidosis). Proporcional a V porque cuanto más sustrato viable queda, más hay para degradarse. La temperatura modula la velocidad vía k(T). |
| (no hay término +) | — | En cold storage estática no existe reparación. Se elimina del modelo base. *Si se modela perfusión normotérmica tipo OCS, aparece un término +λ·(V_max − V) de “sostén metabólico”, pero queda fuera de este TP.* |

### 2.3 Parámetros y constantes — calibración con literatura

**Q₁₀** para tejido cardíaco: literatura reporta Q₁₀ ≈ 2 (regla general de Van’t Hoff, hipotermia neuroprotectora) a Q₁₀ ≈ 3.5 (ATPasa Ca²⁺-activada en miocardio de rata). Para este TP fijamos un valor intermedio basado en la calibración con tiempos clínicos:

**Calibración de k a partir de datos clínicos** (asumiendo que “tiempo máximo aceptado” equivale a V cayendo al ~50 %, V_min ≈ 0.5):

- **A 37 °C** (isquemia caliente sin cardioplegia): viabilidad ~50 % en **30 min** → k(37) ≈ ln(2) / 0.5 h ≈ **1.39 h⁻¹**
- **A 4 °C** (cold static storage): viabilidad ~50 % en **6 h** → k(4) ≈ ln(2) / 6 h ≈ **0.116 h⁻¹**

De ahí se despeja Q₁₀:

$$ \frac{k(37)}{k(4)} = Q_{10}^{(37-4)/10} = Q_{10}^{3.3} \;\;\Rightarrow\;\; Q_{10} \approx (1.39/0.116)^{1/3.3} \approx 2.18 $$

Es decir, **Q₁₀ ≈ 2.2** es consistente con los tiempos clínicos publicados y cae dentro del rango biológico estándar. Tomamos por defecto:

| Parámetro | Valor de trabajo | Fuente |
|---|---|---|
| k_ref (a T_ref = 37 °C) | 1.39 h⁻¹ | Calibrado con WIT cardíaca ~30 min |
| Q₁₀ | 2.2 (rango 2.0–3.0 para análisis de sensibilidad) | Van’t Hoff + ATPasa miocárdica |
| T_ref | 37 °C | Temperatura corporal |
| V_min (umbral clínico) | 0.5 | Criterio práctico: corte donde el riesgo de PGD se vuelve significativo |

### 2.4 Forma final del modelo

**EDO base (T constante durante el transporte):**

$$ \frac{dV}{dt} = -\,k_{ref}\,Q_{10}^{(T - T_{ref})/10}\,V , \qquad V(0)=1 $$

**EDO generalizada (perfil térmico variable T(t), uso operacional real):**

$$ \frac{dV}{dt} = -\,k_{ref}\,Q_{10}^{(T(t) - T_{ref})/10}\,V , \qquad V(0)=1 $$

La generalizada se resuelve numéricamente (Euler — punto 7); la base es **separable** y tiene solución analítica cerrada (puntos 3–5).

### 2.5 Tipo de ecuación

- **Orden:** 1
- **Linealidad:** lineal en V (dV/dt + k(T)·V = 0)
- **Tipo:** **variables separables** (y también lineal homogénea). Ambos métodos analíticos del programa se pueden aplicar.

---

## 3. Datos de literatura disponibles (sustento del modelo)

Lo investigado para que el modelo no quede como “juguete teórico” sino calibrado con números reales:

### 3.1 Tiempos de isquemia clínicamente aceptados (corazón)

| Régimen | Temperatura | Tiempo tolerable | Fuente |
|---|---|---|---|
| Isquemia caliente, sin cardioplegia | ~37 °C | 15 min | Myocardial Protection (StatPearls) |
| Isquemia caliente, con cardioplegia a 37 °C | ~37 °C | 45 min | Frontiers in Physiology / hipotermia ischemia |
| **Cold static storage estándar** | **4 °C** | **4–6 h** | ISHLT, Donor Alliance, múltiples revisiones |
| Cold storage con UW prolongado | 4 °C | hasta 8–10 h (donantes jóvenes) | Frontiers Cardiovasc Med 2023 |
| **OCS normothermic (TransMedics)** | 33–37 °C perfundido | 6–12 h clínico (>16 h experimental) | OCS Heart EXPAND trial |

### 3.2 Reducción metabólica con temperatura

- **Regla Q₁₀ general:** ~50 % menos metabolismo por cada 10 °C de bajada (entre 25–37 °C).
- **A 4 °C:** consumo de O₂ miocárdico se reduce **~97 %** respecto del basal a 37 °C (combinando cardioplegia + hipotermia profunda).
- **Q₁₀ medido para ATPasa miocárdica:** ~3.5 (Impact of temperature on cross-bridge cycling kinetics).

### 3.3 Pérdida de ATP durante isquemia (sin cardioplegia, normotermia)

- **10 min de isquemia normotérmica:** pérdida del **61 %** del ATP subendocárdico.
- **40 min de isquemia normotérmica:** pérdida del **87 %** del ATP.
- Esto valida cualitativamente el orden de magnitud de k(37) ≈ 1.39 h⁻¹ usado en la calibración (61 % de pérdida en 10 min ⇒ k ≈ ln(1/0.39)/(1/6) ≈ 5.7 h⁻¹ si modelamos ATP directamente; el ATP cae más rápido que la “viabilidad agregada” porque hay reservas de fosfocreatina que sostienen V todavía un rato).

### 3.4 PGD (disfunción primaria del injerto) vs. CIT

- Incidencia general de PGD: **2.3 % a 28.2 %** según definición; PGD severa **~10.5 %** (metaanálisis 2024).
- **Cada hora adicional de CIT casi duplica el riesgo de PGD severa.**
- Mortalidad 30 días asociada a PGD severa: **~38.6 %**.
- Estratificación UNOS: limitado 0–3.49 h, prolongado 3.50–6.24 h, extendido ≥6.25 h.
- Donantes <20 años toleran >6 h; donantes >33 años, <3.5 h ideal.

> Este escalonamiento del riesgo sugiere que **el modelo exponencial puro subestima** la zona crítica más allá de las 5–6 h. Una mejora futura sería un modelo con **umbral** (logístico/sigmoide) o agregar un término de daño acumulado por reperfusión. Lo dejamos planteado, fuera del alcance del TP (que pide EDO de primer orden).

### 3.5 Datasets/sensores reales potencialmente accesibles

Para una eventual validación experimental (no requerida por el TP, pero útil mencionarlo):

- **Paragonix SherpaPak / GUARDIAN-Heart Registry:** contenedores instrumentados con loggers de temperatura cada 30 s; ~1500 corazones registrados con outcomes.
- **TransMedics OCS:** publica datos de presión aórtica, flujo coronario, lactato durante el transporte normotérmico.
- **UNOS STAR files:** dataset público con CIT y outcomes a 30 días, 1 año, 5 años por trasplante (>50.000 corazones históricos).
- **ISHLT Registry:** outcomes globales por país.

Estos no son necesarios para el TP, pero respaldan que el modelo es extensible/validable.

---

## 4. Borradores de funciones diferenciales (para puntos 3–7)

### 4.1 Punto 3 — Resolución analítica (separable)

EDO: **dV/dt = −k(T)·V**, con k(T) constante (porque T es constante durante el transporte ideal).

Separamos variables:

$$ \frac{dV}{V} = -k(T)\, dt $$

Integramos ambos lados:

$$ \int \frac{dV}{V} = -k(T) \int dt \;\;\Rightarrow\;\; \ln |V| = -k(T)\,t + C_1 $$

Despejamos V:

$$ V(t) = e^{-k(T)\,t + C_1} = C \cdot e^{-k(T)\,t} $$

### 4.2 Punto 4 — Solución general

$$ \boxed{\; V(t) = C \cdot e^{-k(T)\,t} \;} $$

**Familia de curvas:** exponenciales decrecientes, una por cada valor de C (estado inicial) y cada T (pendiente de la familia). Geométricamente, varían en altura inicial (C) y “rapidez” del decaimiento (k(T)).

**Interpretación física:** todos los órganos que parten de cualquier nivel de viabilidad C decaen exponencialmente; cuanto más fría la temperatura, más “horizontal” la curva (mejor preservación).

### 4.3 Punto 5 — Solución particular (PVI)

Condición inicial realista: **V(0) = 1** (órgano íntegro al momento del clampeo aórtico).

$$ V(0) = C \cdot e^{0} = C = 1 $$

Por lo tanto:

$$ \boxed{\; V(t) = e^{-k(T)\,t} \;} $$

Con valores de trabajo:
- A **T = 4 °C:** V(t) = e^(−0.116·t). V(4 h) ≈ 0.628; V(6 h) ≈ 0.500; V(8 h) ≈ 0.395.
- A **T = 8 °C** (falla térmica leve): k(8) = 1.39 · 2.2^((8−37)/10) ≈ 0.156 h⁻¹. V(6 h) ≈ 0.391. *Sólo 4 °C extra acorta el tiempo útil ~25 %.*
- A **T = 20 °C** (fallo grave del contenedor): k(20) ≈ 0.469 h⁻¹. V(1 h) ≈ 0.626. *El órgano se vuelve marginal en ~1.5 h.*

### 4.4 Punto 6 — Análisis cualitativo / campo de direcciones

- **Pendientes** dV/dt = −k(T)·V: siempre negativas (V>0). Más empinadas para V grande y T alto.
- **Isoclinas:** rectas horizontales V = constante. Para cada V₀, todas las pendientes son iguales sobre la horizontal (independencia de t en el lado derecho).
- **Punto de equilibrio:** **V = 0** (asíntota horizontal). Es atractor: todas las trayectorias convergen al cero.
- **Interpretación física:** el equilibrio “V = 0” representa el órgano completamente inviable. Es un equilibrio estable: una vez extraído, sin perfusión, **el destino inevitable es la inviabilidad total**. La temperatura sólo controla *cuán rápido* se llega ahí. No existe un equilibrio “de preservación indefinida” en cold static storage.

### 4.5 Punto 7 — Simulación numérica (Euler) — pseudocódigo

```python
import numpy as np
import matplotlib.pyplot as plt

# Parámetros calibrados
k_ref = 1.39         # h^-1, a T_ref = 37 °C
T_ref = 37.0         # °C
Q10   = 2.2

def k(T):
    return k_ref * Q10 ** ((T - T_ref) / 10.0)

def euler(V0, T, t_end, dt):
    n = int(t_end / dt)
    ts = np.linspace(0, t_end, n+1)
    Vs = np.zeros(n+1)
    Vs[0] = V0
    for i in range(n):
        Vs[i+1] = Vs[i] + dt * (-k(T) * Vs[i])
    return ts, Vs

# Caso: corazón a 4 °C, 10 horas
ts, Vs_euler = euler(V0=1.0, T=4.0, t_end=10.0, dt=0.1)

# Solución analítica para comparar
Vs_exacta = np.exp(-k(4.0) * ts)

# Campo de direcciones
t_grid, V_grid = np.meshgrid(np.linspace(0, 10, 25),
                             np.linspace(0, 1.05, 20))
dV = -k(4.0) * V_grid
dT = np.ones_like(dV)
norm = np.sqrt(dT**2 + dV**2)

plt.quiver(t_grid, V_grid, dT/norm, dV/norm, alpha=0.4)
plt.plot(ts, Vs_exacta, label='Solución exacta', linewidth=2)
plt.plot(ts, Vs_euler, 'o', label='Euler (dt=0.1h)', markersize=3)
plt.axhline(0.5, ls='--', color='r', label='Umbral V_min=0.5')
plt.xlabel('Tiempo desde clampeo (h)')
plt.ylabel('Viabilidad V(t)')
plt.title('Decaimiento de viabilidad cardíaca a 4 °C')
plt.legend(); plt.grid(); plt.show()
```

**Variante para perfil térmico real** (no requerida por el enunciado, pero útil para defenderla en la presentación):

```python
def T_profile(t):
    # Perfil simulado: 4°C ideal, salto a 10°C entre t=2h y t=3h (demora en pista)
    if 2.0 <= t <= 3.0:
        return 10.0
    return 4.0

def euler_T_variable(V0, t_end, dt):
    n = int(t_end / dt)
    ts = np.linspace(0, t_end, n+1)
    Vs = np.zeros(n+1); Vs[0] = V0
    for i in range(n):
        Vs[i+1] = Vs[i] + dt * (-k(T_profile(ts[i])) * Vs[i])
    return ts, Vs
```

Este segundo script es lo que **conecta el TP con el caso de uso real** del proyecto Casa Justina.

---

## 5. Aclaraciones y decisiones a confirmar antes de seguir

Lo que ya quedó decidido y se asume para el resto del TP:

1. **Órgano analizado:** corazón (caso más restrictivo logísticamente).
2. **Régimen modelado:** cold static storage (preservación estática en frío); se ignora OCS/normotermia.
3. **V(t)** se interpreta como viabilidad agregada normalizada (1 = íntegro, 0 = inviable).
4. **Q₁₀ = 2.2** (calibrado), **k_ref = 1.39 h⁻¹** a 37 °C, **T_ref = 37 °C**.
5. **PVI:** V(0) = 1 al momento del clampeo aórtico.
6. **Umbral clínico:** V_min ≈ 0.5 (se grafica como referencia).

Lo que conviene **confirmar con la cátedra** antes de cerrar la presentación:

- ¿Aceptan presentar k(T) como sub-modelo de Van’t Hoff dentro del mismo TP, o prefieren que el modelo trabaje con **k constante** (T fijo a 4 °C) y la dependencia con T quede como “extensión”? El TP pide “una EDO de primer orden” y la forma compacta es **dV/dt = −k·V**; la dependencia T entra como justificación del valor de k.
- ¿Querés tratar V como adimensional [0,1] o como % [0,100]? Las gráficas cambian sólo de etiqueta.
- ¿Incluir el modelo extendido con T(t) variable como “punto extra” en la presentación, o dejarlo afuera para no sobrecargar?

Cuando estos puntos estén resueltos, los puntos 3 a 7 se cierran rápido con los borradores de arriba.

---

## 6. Fuentes consultadas (resumen)

- **Tiempos de isquemia y preservación**: Medicina Intensiva (España), Donor Alliance, Nefrología al Día, ISHLT guidelines (vía `sources/05_tiempos_isquemia.md` del repo).
- **Q₁₀ y Van’t Hoff en biología**: Wikipedia *Q10 (temperature coefficient)*; Ecological Modelling 2020 (Mundim et al.); PhysiologyWeb Q10 calculator; biostasis.com (regla práctica neuroprotectora).
- **Hipotermia y protección miocárdica**: StatPearls *Myocardial Protection*; AHA Journals *Physiological Impact of Hypothermia* (2021); Frontiers in Physiology *Hypothermia Prevents Cardiac Dysfunction*.
- **Cinética de ATP cardíaco**: Circulation 2000 *ATP Synthesis During Low-Flow Ischemia*; J Mol Cell Cardiol *Impact of temperature on cross-bridge cycling kinetics*.
- **PGD y CIT**: American Journal of Transplantation 2022 *Primary graft dysfunction after heart transplantation*; metaanálisis 2024 en *Cardiothoracic Transplantation*; Heart Surgery Forum *PGD Following Heart Transplantation*.
- **Ecuación de Arrhenius en preservación**: Frontiers Cardiovasc Med 2023 *Graft preservation in heart transplantation*.
- **OCS / TransMedics**: Frontiers Cardiovasc Med 2024 *Extending heart preservation to 24 h*; PMC *Ex Vivo Heart Perfusion ... Initial Experience in the United States*.
