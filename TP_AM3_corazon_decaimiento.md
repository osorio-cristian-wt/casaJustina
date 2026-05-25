# TP Integrador AM III - Modelo de decaimiento de viabilidad cardíaca

> Trabajo Práctico Integrador - Unidad N° 1: Ecuaciones Diferenciales Ordinarias
> Análisis Matemático III - Ingeniería en Sistemas - UAP
> **Nota:** este TP se apoya en la investigación en desarrollo del repositorio [casaJustina](https://github.com/osorio-cristian-wt/casaJustina/tree/AnalisisIII) (logística de trasplantes), del que tomamos los datos clínicos y el contexto del problema.

**Nombres de los alumnos:**
- ⦁ Peter Jared
- ⦁ Rosales Alan
- ⦁ Osorio Cristian

---

## 0. ¿De qué trata todo esto?

En el sistema argentino de trasplantes, cada órgano tiene una ventana de tiempo muy acotada para llegar al receptor. El corazón es el más exigente: aguanta apenas **4 a 6 horas** desde la extracción hasta el implante, lo que en la práctica limita el radio de transporte (Buenos Aires ↔ Mendoza ya está en el filo, ver [tiempos de isquemia](https://github.com/osorio-cristian-wt/casaJustina/blob/AnalisisIII/sources/05_tiempos_isquemia.md)).

Hoy esa ventana se trata como un valor fijo, sin tener en cuenta **cómo varía la viabilidad real según la temperatura del contenedor**. Si pudiéramos calcular el decaimiento en tiempo real (con sensores de temperatura desde el momento mismo del paro del donante), se podría:

- Estimar dinámicamente cuánto margen de transporte queda.
- Comparar rutas con distinto perfil de temperatura y tiempo.
- Avisar antes de que un operativo se pase del umbral de viabilidad.
- Justificar con números si extender o acortar el “4–6 h” genérico.

Lo que sigue es justamente eso: el **modelo matemático base** (una EDO de primer orden) que da pie a esa funcionalidad.

---

# Punto 1 - Selección y contextualización del problema

## 1.1 Fenómeno seleccionado

Modelamos el **decaimiento de la viabilidad de un órgano (caso: corazón) desde el momento del paro circulatorio del donante hasta el implante en el receptor**, en función del tiempo transcurrido y de la temperatura a la que el órgano está expuesto en cada fase.

Un detalle importante: el reloj **no arranca con la extracción**, sino con el paro circulatorio (por ejemplo, en un accidente de tránsito, o en una muerte encefálica seguida de paro). Desde ese instante el órgano queda sin perfusión efectiva y el daño empieza a acumularse. La temperatura va cambiando a lo largo del proceso (cuerpo todavía tibio → enfriamiento in situ → cardioplegia a 4 °C → transporte → implante), y **cada fase con su temperatura aporta su propio daño** al total.

## 1.2 Descripción física/biológica

Al cesar la perfusión en el cuerpo del donante, las células miocárdicas dejan de recibir oxígeno y sustratos. Independientemente de la temperatura:

1. La fosforilación oxidativa se detiene → **caída del ATP**.
2. Las bombas Na⁺/K⁺-ATPasa fallan → **edema celular** y desequilibrio iónico.
3. Se acumula lactato, baja el pH (de 7.4 a ~6.0).
4. Al reperfundir en el receptor, se desencadena **daño por isquemia-reperfusión** (radicales libres, sobrecarga de Ca²⁺, apertura del MPTP mitocondrial).

La **temperatura controla la velocidad de todos estos procesos**: enfriar a 4 °C reduce el metabolismo basal a ~3–5 % del valor a 37 °C (ley de Van’t Hoff / Q₁₀ ≈ 2–3 en tejidos biológicos). Por eso la preservación estándar es **cold static storage** con soluciones cardiopléjicas (Custodiol/HTK, Celsior, UW) a 4 °C. La idea no es que el frío *detenga* el daño, sino que lo enlentece unas 10 a 20 veces.

## 1.3 Fases del proceso (cronología real)

| Fase | Cuándo | Temperatura típica | Duración típica |
|---|---|---|---|
| **0. Pre-muerte** | Antes del paro | 37 °C, perfusión normal | n/a (V=1 inicial) |
| **1. Isquemia caliente in situ (WIT)** | Desde paro circulatorio hasta primer enfriamiento | 25–37 °C (cuerpo enfriándose) | 5–30 min (variable, crítico) |
| **2. Enfriamiento + extracción** | Cardioplegia fría in situ + cirugía | 4–15 °C | 10–30 min |
| **3. Cold storage (CIT)** | Transporte estático en hielo | 4 °C | 1–6 h |
| **4. Re-implante y reperfusión** | Anastomosis + restablecimiento de flujo | sube a 37 °C | 30–60 min |

El TP modela **fases 1 + 2 + 3** (todo lo que ocurre con el órgano sin perfusión efectiva). La fase 4 (reperfusión) tiene dinámica propia y queda fuera.

## 1.4 Simplificaciones del modelo (variables que dejamos afuera)

El fenómeno real depende de **muchas más variables** que las que vamos a usar. Las listamos para ser transparentes con el alcance del TP:

| Variable real | Por qué influye | Cómo la tratamos en el TP |
|---|---|---|
| Edad del donante | Donantes <20 años toleran >6 h CIT; >33 años, <3.5 h | Ignorada (asumimos donante “promedio”) |
| Comorbilidades del donante | Diabetes, HTA, sepsis aceleran el deterioro | Ignorada |
| Tipo de solución de preservación (UW, HTK, Celsior) | Cambia la cinética en ~20–40 % | Asumimos solución estándar; absorbida en k_ref |
| Presión, pH, lactato del órgano | Marcadores bioquímicos del estado real | No medidos; se aproximan agregadamente vía V |
| Daño previo (paro prolongado, RCP) | Modifica V₀ | Asumimos V₀ = 1 (órgano íntegro al paro) |
| Variación intra-órgano de temperatura | Centro vs. superficie tardan distinto en enfriarse | Asumimos T uniforme (modelo de un compartimento) |
| Reperfusión (radicales libres, Ca²⁺) | Puede *aumentar* el daño al implantar | Modelo termina antes del implante |
| Heterogeneidad celular | Endotelio, miocitos, conducción tienen cinéticas distintas | Una sola variable agregada V |

**Decisión metodológica:** para que el modelo sea **una EDO de primer orden tratable analíticamente** (que es lo que pide el TP), nos quedamos con sólo **tres variables: tiempo (t), temperatura (T) y viabilidad (V)**. Todo lo demás se promedia o se absorbe en los parámetros calibrados (k_ref, Q₁₀). La idea es capturar la **dinámica esencial** del fenómeno, no reproducir toda su complejidad bioquímica.

## 1.5 Variables del modelo

| Símbolo | Variable | Tipo | Unidad |
|---|---|---|---|
| **t** | Tiempo desde el paro circulatorio | Independiente | horas (h) |
| **T** | Temperatura del órgano | Independiente (parámetro o función T(t)) | grados Celsius (°C) |
| **V(t)** | Viabilidad del órgano (fracción funcional respecto del estado inicial) | Dependiente | adimensional, V ∈ [0, 1] |
| **k(T)** | Constante de degradación a temperatura T | Parámetro | h⁻¹ |
| **k_ref** | Constante de degradación a temperatura de referencia | Parámetro | h⁻¹ |
| **T_ref** | Temperatura de referencia (37 °C) | Constante | °C |
| **Q₁₀** | Coeficiente de Van’t Hoff (factor por cada 10 °C) | Parámetro | adimensional |
| **V_min** | Umbral mínimo de viabilidad para trasplante exitoso | Parámetro | adimensional (~0.5 según criterio) |

**Interpretación de V(t):** métrica agregada normalizada (V=1 al paro, V=0 = inviable). En la práctica clínica se aproxima combinando ATP residual, integridad de membrana (LDH), edema, función contráctil post-reperfusión. Para el modelo teórico la tratamos como una sola variable de estado.

## 1.6 Por qué tiene sentido computarizar este modelo

La clave operacional es lo que llamamos **encadenamiento**: el daño acumulado durante una fase se *arrastra* a la siguiente, porque la viabilidad al final de una fase es la condición inicial de la siguiente.

> *Ejemplo: una persona fallece en un accidente y el órgano queda a ~25 °C durante 15 min hasta que el equipo médico llega y aplica cardioplegia. ¿Cuánto tiempo de transporte a 4 °C le queda al corazón antes de cruzar el umbral de viabilidad?*

El modelo permite responder esa pregunta de forma exacta (ver Apéndice A). Posibles usos prácticos: ruteo dinámico, reasignación del receptor, alertas tempranas, y una métrica de “tiempo equivalente” para comparar perfiles térmicos distintos en una sola escala.

---

# Punto 2 - Modelización matemática

## 2.1 Hipótesis del modelo

**H1.** La velocidad de pérdida de viabilidad es proporcional a la viabilidad actual (modelo de primer orden, análogo a la desintegración radiactiva y a la ley de enfriamiento de Newton):
> *Cuanto más viable está el órgano, más sustrato hay para degradar.* Es la hipótesis más simple compatible con la literatura de cinética celular.

**H2.** La constante de proporcionalidad **k(T)** depende exclusivamente de la temperatura, según la ley de Van’t Hoff (equivalente “suave” a la ecuación de Arrhenius en el rango fisiológico 0–40 °C).

**H3.** No hay términos que sumen viabilidad: en isquemia (caliente o fría) sin perfusión activa **sólo hay pérdida**, no hay reparación.

**H4.** Temperatura uniforme en el órgano (modelo de un compartimento; razonable porque el corazón se enfría en ~10 min en solución fría).

**H5.** Se ignora la fase de reperfusión (que tiene su propia dinámica). El modelo termina cuando se reperfunde en el receptor.

**H6 (encadenamiento).** El proceso real se descompone en fases con temperatura aproximadamente constante (T₁, T₂, …, Tₙ). La viabilidad al final de una fase es la **condición inicial** de la fase siguiente: V_i(0) ≡ V_{i−1}(Δt_{i−1}). Esto sale automáticamente de la EDO porque el lado derecho no depende explícitamente de t.

## 2.2 Formulación de la EDO

Aplicando H1 + H3:

$$ \boxed{\;\frac{dV}{dt} = -\,k(T)\cdot V\;} $$

con condición inicial **V(0) = V₀ = 1** (órgano íntegro al momento del paro circulatorio).

**Términos del modelo:**

| Término | Signo | Justificación física |
|---|---|---|
| **−k(T)·V** | resta | Degradación metabólica (ATP↓, edema, acidosis). Proporcional a V porque cuanto más sustrato viable queda, más hay para degradarse. La temperatura modula la velocidad vía k(T). |
| (no hay término +) | - | En isquemia estática no existe reparación. Se elimina del modelo base. |

## 2.3 Modelo de k(T) - ley de Van’t Hoff / Q₁₀

$$ k(T) = k_{ref}\cdot Q_{10}^{(T - T_{ref})/10} $$

**Interpretación:** k_ref es la velocidad de degradación a la temperatura de referencia T_ref (37 °C). Q₁₀ es el factor por el cual k cambia cada 10 °C de variación de T. Por cada 10 °C que se enfría el órgano, la velocidad de degradación se divide por Q₁₀ (y viceversa para calentamiento).

## 2.4 Calibración con datos clínicos

Asumimos que el “tiempo máximo aceptado” en la práctica clínica equivale al instante en que V cruza V_min ≈ 0.5:

- **A 37 °C** (isquemia caliente sin cardioplegia): viabilidad ~50 % en **30 min** → k(37) ≈ ln(2) / 0.5 h ≈ **1.39 h⁻¹**
- **A 4 °C** (cold static storage): viabilidad ~50 % en **6 h** → k(4) ≈ ln(2) / 6 h ≈ **0.116 h⁻¹**

De ahí se despeja Q₁₀:

$$ \frac{k(37)}{k(4)} = Q_{10}^{(37-4)/10} = Q_{10}^{3.3} \;\;\Rightarrow\;\; Q_{10} \approx (1.39/0.116)^{1/3.3} \approx 2.18 $$

**Q₁₀ ≈ 2.2** es consistente con los tiempos clínicos publicados y cae dentro del rango biológico estándar (2–3).

| Parámetro | Valor de trabajo | Fuente |
|---|---|---|
| k_ref (a T_ref = 37 °C) | 1.39 h⁻¹ | Calibrado con WIT cardíaca ~30 min |
| Q₁₀ | 2.2 (rango 2.0–3.0 para análisis de sensibilidad) | Van’t Hoff + ATPasa miocárdica |
| T_ref | 37 °C | Temperatura corporal |
| V₀ | 1 | Órgano íntegro al paro circulatorio |
| V_min | 0.5 | Umbral clínico práctico |

## 2.5 Tipo de ecuación

- **Orden:** 1
- **Linealidad:** lineal en V (forma normalizada: dV/dt + k(T)·V = 0)
- **Tipo:** **variables separables** y a la vez **lineal homogénea**. Ambos métodos analíticos del programa son aplicables.

---

# Punto 3 - Resolución analítica de la EDO

La EDO es de **variables separables**. Aplicamos el método correspondiente.

**Paso 1 - Reordenar separando V y t:**

$$ \frac{dV}{dt} = -k(T)\cdot V \;\;\Longrightarrow\;\; \frac{dV}{V} = -k(T)\, dt $$

**Paso 2 - Integrar ambos lados:**

$$ \int \frac{dV}{V} = \int -k(T)\, dt $$

$$ \ln|V| = -k(T)\cdot t + C_1 $$

con C₁ constante de integración.

**Paso 3 - Despejar V aplicando la exponencial:**

$$ |V| = e^{-k(T)\, t + C_1} = e^{C_1}\cdot e^{-k(T)\,t} $$

Como V > 0 en todo instante físicamente significativo, podemos quitar el módulo. Renombrando la constante e^{C₁} ≡ C:

$$ V(t) = C\cdot e^{-k(T)\,t} $$

**Verificación por método del factor integrante** (chequeo cruzado): la EDO en forma normalizada es

$$ \frac{dV}{dt} + k(T)\cdot V = 0 $$

El factor integrante es μ(t) = e^{k(T)·t}. Multiplicando:

$$ \frac{d}{dt}\!\left[e^{k(T)\,t}\cdot V\right] = 0 \;\;\Rightarrow\;\; e^{k(T)\,t}\cdot V = C \;\;\Rightarrow\;\; V(t) = C\cdot e^{-k(T)\,t} $$

Coincide con el resultado por separación de variables. ✓

---

# Punto 4 - Solución general

$$ \boxed{\; V(t) = C\cdot e^{-k(T)\,t} \;} $$

## Familia de curvas

La solución general representa una **familia uniparamétrica de exponenciales decrecientes**, indexada por la constante C ∈ ℝ⁺ (que fija la viabilidad inicial). A un T fijo, todas las curvas comparten la misma rapidez de caída (la pendiente relativa dV/V dt = −k(T) es la misma para todas), y se diferencian sólo por su altura inicial.

Si se varía también T, se obtiene una **familia bi-paramétrica** donde:
- C controla la altura inicial.
- T controla la “planicidad” de la curva (mayor T → curva más empinada; menor T → curva más horizontal).

## Interpretación

- **Matemática:** soluciones de una EDO lineal homogénea de primer orden con coeficiente constante; sus gráficas son exponenciales decrecientes que se aproximan asintóticamente a V = 0.
- **Física/biológica:** todos los órganos parten de algún nivel de viabilidad C y decaen exponencialmente; cuanto más fría la temperatura, más lentamente. Ningún órgano se preserva indefinidamente en cold static storage: V → 0 cuando t → ∞.

---

# Punto 5 - Solución particular (Problema de Valor Inicial)

## Planteo del PVI

$$ \begin{cases} \dfrac{dV}{dt} = -k(T)\cdot V \\[4pt] V(0) = 1 \end{cases} $$

**Condición inicial física:** al instante t = 0 (paro circulatorio del donante) el órgano está íntegro, por lo que V(0) = V₀ = 1 (100 % de viabilidad).

## Cálculo de la constante

A partir de la solución general V(t) = C·e^{−k(T)·t} y la condición inicial:

$$ V(0) = C\cdot e^{0} = C = 1 $$

## Solución particular

$$ \boxed{\; V(t) = e^{-k(T)\,t} \;} $$

## Evaluación numérica con los parámetros calibrados

A diferentes temperaturas de preservación, usando Q₁₀ = 2.2 y k_ref = 1.39 h⁻¹:

| T (°C) | k(T) [h⁻¹] | V(1 h) | V(4 h) | V(6 h) | Tiempo hasta V=0.5 |
|---|---|---|---|---|---|
| 4 | 0.116 | 0.890 | 0.628 | 0.500 | **5.97 h** |
| 8 | 0.156 | 0.855 | 0.535 | 0.391 | **4.45 h** |
| 15 | 0.252 | 0.777 | 0.366 | 0.222 | **2.75 h** |
| 25 | 0.549 | 0.578 | 0.111 | 0.037 | **1.26 h** |
| 37 | 1.390 | 0.249 | 0.004 | < 0.001 | **0.50 h** |

> Lectura: un salto de 4 °C → 8 °C en el contenedor (falla térmica suave) reduce el tiempo útil en ~25 %.
> Un salto a 25 °C (falla grave) lo reduce en ~80 %.

---

# Punto 6 - Campo de direcciones y análisis cualitativo

## Estructura del campo

Para la EDO dV/dt = −k(T)·V con T fijo (por ejemplo 4 °C, k ≈ 0.116 h⁻¹):

- El lado derecho **no depende de t** (sistema autónomo). Esto implica que las pendientes son **constantes a lo largo de cualquier línea horizontal V = cte** → las **isoclinas son rectas horizontales**.
- En cada isoclina V = V*, la pendiente vale −k(T)·V*. Mayor V → pendiente más negativa (caída más rápida); V cercano a 0 → pendiente casi nula (curva casi plana).
- Para V > 0 las pendientes son siempre negativas → todas las trayectorias decrecen.
- Para V < 0 las pendientes serían positivas, pero esa región carece de sentido físico (V negativa no existe).

## Puntos de equilibrio

Buscamos dV/dt = 0:

$$ -k(T)\cdot V = 0 \;\;\Rightarrow\;\; V = 0 $$

Existe un **único punto de equilibrio: V* = 0**, que aparece como **asíntota horizontal** en el campo de direcciones.

**Estabilidad:** como k(T) > 0 para toda T > 0 K, cualquier perturbación V > 0 se mueve *hacia* V = 0 (las pendientes apuntan hacia abajo). El equilibrio es **asintóticamente estable** (atractor global en V ≥ 0).

## Significado físico del equilibrio

**V = 0 representa el órgano completamente inviable.** Es un equilibrio estable: una vez extraído y sin perfusión, el destino inevitable es la inviabilidad total. La temperatura sólo controla **qué tan rápido** se llega ahí; ninguna T (por baja que sea) genera un equilibrio “de preservación indefinida” mientras el órgano esté en cold static storage.

Esto coincide con la realidad clínica: incluso con hielo seco profundo, el corazón no se conserva más de ~8–10 h. Para extender la viabilidad en serio (>12 h) hay que cambiar la **estructura** del modelo, no sólo el parámetro T: haría falta perfusión normotérmica (tipo TransMedics OCS), que sumaría un término +λ(V_max − V) de “sostén metabólico” y crearía un equilibrio nuevo con V* > 0.

---

# Punto 7 - Simulación numérica con Método de Euler

## 7.1 Algoritmo de Euler implementado desde cero

El método de Euler explícito aproxima la solución de una EDO de primer orden y′ = f(t, y) con paso temporal Δt:

$$ V_{i+1} = V_i + \Delta t \cdot f(t_i, V_i) $$

Para nuestra EDO con T constante: f(t, V) = −k(T)·V, por lo que:

$$ V_{i+1} = V_i + \Delta t\cdot(-k(T)\cdot V_i) = V_i\cdot(1 - k(T)\,\Delta t) $$

## 7.2 Script Python (gráfico unificado: campo + exacta + Euler)

```python
import numpy as np
import matplotlib.pyplot as plt

# --- Parámetros calibrados ---
k_ref = 1.39      # h^-1, velocidad de degradación a T_ref
T_ref = 37.0      # °C
Q10   = 2.2       # Coeficiente de Van't Hoff
V_min = 0.5       # Umbral clínico de viabilidad

def k(T):
    """Constante de degradación a temperatura T (Van't Hoff)."""
    return k_ref * Q10 ** ((T - T_ref) / 10.0)

# --- Método de Euler desde cero ---
def euler(V0, T, t_end, dt):
    n = int(t_end / dt)
    ts = np.linspace(0, t_end, n + 1)
    Vs = np.zeros(n + 1)
    Vs[0] = V0
    for i in range(n):
        Vs[i + 1] = Vs[i] + dt * (-k(T) * Vs[i])
    return ts, Vs

# --- Configuración del caso a graficar ---
T_caso = 4.0        # °C - cold storage estándar
t_end  = 10.0       # horas
dt     = 0.5        # paso "grande" para mostrar error de Euler visualmente

# Aproximación de Euler
ts_e, Vs_e = euler(V0=1.0, T=T_caso, t_end=t_end, dt=dt)

# Solución exacta (Punto 5)
ts_x = np.linspace(0, t_end, 400)
Vs_x = np.exp(-k(T_caso) * ts_x)

# --- Campo de direcciones ---
t_grid, V_grid = np.meshgrid(np.linspace(0, t_end, 22),
                             np.linspace(0, 1.1, 18))
dV = -k(T_caso) * V_grid
dT = np.ones_like(dV)
norm = np.sqrt(dT ** 2 + dV ** 2)

# --- Gráfico unificado ---
fig, ax = plt.subplots(figsize=(10, 6))
ax.quiver(t_grid, V_grid, dT / norm, dV / norm,
          color='gray', alpha=0.4, scale=30, width=0.0025,
          label='Campo de direcciones')
ax.plot(ts_x, Vs_x, 'b-', linewidth=2.2,
        label=f'Solución exacta  V(t) = e^(-k·t),  k={k(T_caso):.3f} h⁻¹')
ax.plot(ts_e, Vs_e, 'ro', markersize=7,
        label=f'Euler  (Δt = {dt} h)')
ax.axhline(V_min, color='red', ls='--', alpha=0.6,
           label=f'Umbral V_min = {V_min}')
ax.axhline(0, color='black', ls=':', alpha=0.5,
           label='Equilibrio V* = 0  (asíntota)')
ax.set_xlabel('Tiempo desde paro circulatorio  t  [h]')
ax.set_ylabel('Viabilidad  V(t)  [adim.]')
ax.set_title(f'Decaimiento de viabilidad cardíaca a T = {T_caso} °C\n'
             f'Campo de direcciones + Solución exacta + Método de Euler')
ax.set_xlim(0, t_end); ax.set_ylim(-0.05, 1.15)
ax.legend(loc='upper right'); ax.grid(alpha=0.3)
plt.tight_layout(); plt.show()

# --- Análisis cuantitativo del error de Euler ---
Vs_exacta_en_ts_e = np.exp(-k(T_caso) * ts_e)
error_abs = np.abs(Vs_e - Vs_exacta_en_ts_e)
error_rel = error_abs / Vs_exacta_en_ts_e * 100
print(f"{'t (h)':>8} {'V Euler':>10} {'V exacta':>10} "
      f"{'|error|':>10} {'err %':>8}")
for t_i, ve, vx, ea, er in zip(ts_e, Vs_e, Vs_exacta_en_ts_e,
                                error_abs, error_rel):
    print(f"{t_i:>8.2f} {ve:>10.4f} {vx:>10.4f} "
          f"{ea:>10.4f} {er:>8.2f}")
```

## 7.3 Análisis de la gráfica y precisión del método

**Lo que se ve en la gráfica:**

- El **campo de direcciones** (flechas grises) confirma visualmente lo del Punto 6: las pendientes son constantes sobre cada línea horizontal, apuntan todas hacia abajo, y se van aplanando al acercarse a V = 0 (la asíntota).
- La **curva exacta azul** sigue suavemente la dirección que dicta el campo en cada punto, como es esperable para una solución analítica.
- Los **puntos rojos de Euler** arrancan pegados a la curva exacta pero se van **separando hacia abajo** con el correr del tiempo. El motivo es que Euler usa la pendiente en el punto actual (la más empinada del intervalo) para extrapolar todo el paso, así que termina cayendo un poquito más rápido que la curva real.

**Precisión del método (con Δt = 0.5 h y T = 4 °C):**

| t (h) | V Euler | V exacta | error relativo |
|---|---|---|---|
| 0 | 1.000 | 1.000 | 0.00 % |
| 1 | 0.887 | 0.890 | 0.35 % |
| 2 | 0.787 | 0.793 | 0.68 % |
| 4 | 0.620 | 0.629 | 1.35 % |
| 6 | 0.488 | 0.499 | 2.03 % |
| 10 | 0.303 | 0.314 | 3.41 % |

Para esta EDO, Euler con un paso bastante grueso (Δt = 0.5 h) ya tira un error menor al 3.5 % a 10 horas — más que suficiente para visualizar el fenómeno. Si reducimos a Δt = 0.05 h (3 min, realista para sensores reales) el error a 6 h queda por debajo del 0.3 %. Esto encaja con la teoría: Euler tiene **error local O(Δt²)** y **error global O(Δt)**, así que dividir el paso por 10 divide el error por 10.

**Conclusión sobre Euler:** anda muy bien para prototipar y visualizar este fenómeno, y la propia EDO lineal homogénea es un caso amable (sin rigidez, sin oscilaciones). Para una aplicación clínica real probablemente usaríamos Runge–Kutta de orden 4 o el RK45 de SciPy por defensa en profundidad, pero para los fines del TP, Euler con Δt razonable mantiene todas las propiedades cualitativas correctas (monotonía, asíntota, equilibrio).

---

# Apéndice A - Encadenamiento de fases (caso operacional real)

En la realidad la temperatura no es constante: el órgano pasa por varias fases (cuerpo tibio → cardioplegia → transporte en hielo) y cada una aporta su propio daño. La buena noticia es que el modelo del TP **ya lo soporta sin agregar nada nuevo**: alcanza con aplicar la solución particular fase por fase, usando la viabilidad que sale de una fase como condición inicial de la siguiente (hipótesis H6).

## A.1 Ejemplo trabajado

> Una persona fallece en un accidente y el órgano queda a ~25 °C durante **15 min** hasta que llega el equipo médico y aplica cardioplegia. Después se transporta a 4 °C. **¿Cuánto tiempo de transporte queda hasta que la viabilidad cruce V_min = 0.5?**

**Paso 1 - k de cada fase** (con la fórmula de Van't Hoff del Punto 2.3):
- Fase 1 (WIT a 25 °C): k(25) = 1.39 · 2.2^((25−37)/10) ≈ **0.549 h⁻¹**
- Fase 2 (CIT a 4 °C): k(4) ≈ **0.116 h⁻¹**

**Paso 2 - V al terminar la fase 1** (15 min = 0.25 h):
$$ V_1 = 1 \cdot e^{-0.549 \cdot 0.25} = e^{-0.137} \approx 0.872 $$

El órgano ya perdió ~13 % de viabilidad antes de entrar en frío.

**Paso 3 - Tiempo restante en fase 2** (arrancando ahora de V = 0.872):
$$ 0.5 = 0.872 \cdot e^{-0.116\,t_2} \;\Rightarrow\; t_2 = \frac{\ln(0.872/0.5)}{0.116} \approx \mathbf{4.79\ h} $$

**Comparación con el escenario ideal:**
- Sin WIT (cardioplegia inmediata): tiempo útil a 4 °C = ln(2)/0.116 ≈ **5.97 h**.
- Con 15 min de WIT a 25 °C: tiempo útil restante ≈ **4.79 h**.
- **Costo real de esos 15 min calientes: ~1.18 h menos de transporte disponible.**

Es decir, un retraso aparentemente menor (un cuarto de hora) consume cerca del 20 % del presupuesto total de tiempo. Eso es exactamente lo que justifica computarizar el modelo: una decisión logística que mire sólo "tengo 6 horas desde el frío" se equivoca por hora y pico.

## A.2 Tiempo equivalente (métrica unificada)

Como cada fase aporta un término al exponente, se puede expresar todo el daño como un **tiempo equivalente a una temperatura de referencia**. En el ejemplo, los 15 min a 25 °C equivalen a ~1.18 h de cold storage a 4 °C. Esto permite comparar logísticas heterogéneas (WIT corto + transporte largo vs. WIT largo + transporte corto) en una sola escala.

## A.3 Script Python multi-fase

```python
fases = [
    (25.0, 0.25),   # Fase 1: WIT a 25 °C, 15 min
    ( 4.0, 6.00),   # Fase 2: cold storage a 4 °C, 6 h
]

def euler_multifase(V0, fases, dt=0.01):
    ts_total, Vs_total = [0.0], [V0]
    V_actual, t_actual = V0, 0.0
    for T_i, dur_i in fases:
        n = int(dur_i / dt)
        for _ in range(n):
            V_actual = V_actual + dt * (-k(T_i) * V_actual)
            t_actual += dt
            ts_total.append(t_actual)
            Vs_total.append(V_actual)
    return np.array(ts_total), np.array(Vs_total)

ts, Vs = euler_multifase(V0=1.0, fases=fases)

# Cálculo analítico exacto del tiempo útil restante
V_post_wit = np.exp(-k(25.0) * 0.25)
t_restante = np.log(V_post_wit / 0.5) / k(4.0)
print(f"V al terminar WIT (15 min @ 25 °C): {V_post_wit:.3f}")
print(f"Tiempo útil restante a 4 °C hasta V=0.5: {t_restante:.2f} h")
```

---

# Apéndice B - Datos de literatura para la calibración

## B.1 Tiempos de isquemia clínicamente aceptados (corazón)

| Régimen | Temperatura | Tiempo tolerable | Fuente |
|---|---|---|---|
| Isquemia caliente sin cardioplegia | ~37 °C | 15 min | [StatPearls — *Myocardial Protection*](https://www.ncbi.nlm.nih.gov/books/NBK567795/) |
| Isquemia caliente con cardioplegia a 37 °C | ~37 °C | 45 min | [Frontiers in Physiology — *Hypothermia Prevents Cardiac Dysfunction*](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9301761/) |
| **Cold static storage estándar** | **4 °C** | **4–6 h** | [Frontiers Cardiovasc Med 2023 — *Graft preservation in heart transplantation*](https://www.frontiersin.org/journals/cardiovascular-medicine/articles/10.3389/fcvm.2023.1253579/full) |
| Cold storage con UW prolongado | 4 °C | 8–10 h en donantes jóvenes | [Frontiers Cardiovasc Med 2023](https://www.frontiersin.org/journals/cardiovascular-medicine/articles/10.3389/fcvm.2023.1253579/full) |
| OCS normotérmico (TransMedics) | 33–37 °C perfundido | 6–12 h clínico (>16 h experimental) | [Frontiers Cardiovasc Med 2024 — *Extending heart preservation to 24 h*](https://www.frontiersin.org/journals/cardiovascular-medicine/articles/10.3389/fcvm.2024.1325169/full) |

## B.2 Reducción metabólica con temperatura

- Regla Q₁₀ general: ~50 % menos metabolismo por cada 10 °C de bajada (rango 25–37 °C). Ver [Q10 — Wikipedia](https://en.wikipedia.org/wiki/Q10_(temperature_coefficient)).
- A 4 °C: consumo de O₂ miocárdico se reduce ~97 % respecto del basal a 37 °C. Ver [AHA Journals — *Physiological Impact of Hypothermia*](https://journals.physiology.org/doi/full/10.1152/physiol.00025.2021).
- Q₁₀ medido para ATPasa miocárdica: ~3.5. Ver [PMC — *Impact of temperature on cross-bridge cycling kinetics in rat myocardium*](https://pmc.ncbi.nlm.nih.gov/articles/PMC2277159/).

## B.3 Pérdida de ATP durante isquemia normotérmica

- 10 min: pérdida del 61 % del ATP subendocárdico.
- 40 min: pérdida del 87 % del ATP.

Fuente: [Circulation 2000 — *ATP Synthesis During Low-Flow Ischemia*](https://www.ahajournals.org/doi/10.1161/01.cir.101.17.2090).

## B.4 PGD vs. CIT

- Incidencia general PGD: 2.3 % a 28.2 %; PGD severa ~10.5 %.
- **Cada hora adicional de CIT casi duplica el riesgo de PGD severa.**
- Mortalidad 30 días asociada a PGD severa: ~38.6 %.
- Categorías UNOS: limitado 0–3.49 h, prolongado 3.50–6.24 h, extendido ≥6.25 h.
- Donantes <20 años toleran >6 h; donantes >33 años, <3.5 h ideal.

Fuentes: [American Journal of Transplantation 2022 — *Primary graft dysfunction after heart transplantation*](https://www.amjtransplant.org/article/S1600-6135(22)09577-6/fulltext); [PMC — Metaanálisis de factores de riesgo de PGD (2024)](https://pmc.ncbi.nlm.nih.gov/articles/PMC13019779/).

> Este escalonamiento del riesgo sugiere que el modelo exponencial puro **subestima** la zona crítica más allá de 5–6 h. Mejora futura: modelo con umbral (logístico/sigmoide) o término extra de daño acumulado por reperfusión. Queda fuera del alcance de un TP de primer orden.

---

# Apéndice C - Fuentes consultadas

- **Tiempos de isquemia y preservación**: Medicina Intensiva (España), Donor Alliance, Nefrología al Día, ISHLT guidelines (vía `sources/05_tiempos_isquemia.md` del repo).
- **Q₁₀ y Van’t Hoff en biología**: Wikipedia *Q10 (temperature coefficient)*; Ecological Modelling 2020 (Mundim et al.); PhysiologyWeb Q10 calculator.
- **Hipotermia y protección miocárdica**: StatPearls *Myocardial Protection*; AHA Journals *Physiological Impact of Hypothermia* (2021); Frontiers in Physiology *Hypothermia Prevents Cardiac Dysfunction*.
- **Cinética de ATP cardíaco**: Circulation 2000 *ATP Synthesis During Low-Flow Ischemia*; J Mol Cell Cardiol *Impact of temperature on cross-bridge cycling kinetics*.
- **PGD y CIT**: American Journal of Transplantation 2022 *Primary graft dysfunction after heart transplantation*; metaanálisis 2024 en *Cardiothoracic Transplantation*.
- **Ecuación de Arrhenius en preservación**: Frontiers Cardiovasc Med 2023 *Graft preservation in heart transplantation*.
- **OCS / TransMedics**: Frontiers Cardiovasc Med 2024 *Extending heart preservation to 24 h*; PMC *Ex Vivo Heart Perfusion Initial Experience US*.
