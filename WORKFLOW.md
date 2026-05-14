# WORKFLOW — Cómo investigar y documentar en este repositorio

> Esta es la **metodología replicable** que usamos para producir el contenido actual de este repo.
> Si vas a continuar la investigación (vos o un compañero del equipo, o usando Claude/otro LLM), seguí este flujo.

---

## 0. Principios

1. **Una fuente → un archivo.** Nada de meter 3 URLs en un solo archivo. Cada fuente tiene contexto, fecha de consulta y autoría.
2. **Toda síntesis cita fuentes.** Si afirmás algo en `research.md` o en un `analisis/`, debe poder rastrearse a un archivo de `sources/`.
3. **Plantillas iguales.** Las fuentes tienen una plantilla; los análisis tienen otra. No improvisar formato.
4. **Markdown plano.** Sin extensiones raras. Sin imágenes en remoto si se puede evitar. Pensado para ser leído en GitHub, VS Code o cualquier viewer.
5. **Tablas comparativas siempre que se pueda.** Más fácil de auditar que prosa.
6. **No prosa vacía.** Cada párrafo debe agregar algo verificable o accionable.
7. **Dudás de un dato → marcarlo como tal.** Mejor "estimado / a verificar" que afirmar falso.

---

## 1. Fases de la investigación

```
┌─────────────────────────────────────────────────────────────────┐
│ FASE 1 — RECONNAISSANCE (broad)                                 │
│ Objetivo: mapear el espacio del problema                        │
│ Output: 10-20 búsquedas web, lista de fuentes candidatas        │
└─────────────────────────────┬───────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ FASE 2 — DEEP DIVE (vertical)                                   │
│ Objetivo: leer cada fuente clave a fondo                        │
│ Output: 1 archivo sources/NN_*.md por fuente                    │
└─────────────────────────────┬───────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ FASE 3 — SYNTHESIS (transversal)                                │
│ Objetivo: identificar patrones, hipótesis, contradicciones      │
│ Output: research.md (acumulativo)                               │
└─────────────────────────────┬───────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ FASE 4 — CRITICAL ANALYSIS (sobre una propuesta concreta)       │
│ Objetivo: evaluar viabilidad técnica + legal + económica        │
│ Output: 1 archivo analisis/NN_*.md por propuesta                │
└─────────────────────────────┬───────────────────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ FASE 5 — DECISIONES + NEXT STEPS                                │
│ Objetivo: convertir análisis en acción                          │
│ Output: actualización del README + tareas concretas             │
└─────────────────────────────────────────────────────────────────┘
```

Iterá las fases. La investigación no es lineal. Si en Fase 4 descubrís que falta información, volvé a Fase 2 con búsquedas dirigidas.

---

## 2. Plantilla para `sources/NN_nombre.md`

Cada fuente debe seguir esta estructura **exacta**:

```markdown
# <Título descriptivo de la fuente>

- **URL:** <link>
- **URL secundaria (opcional):** <link>
- **Fecha de consulta:** YYYY-MM-DD
- **Tipo:** <oficial / técnico / prensa / académico / startup / norma>

## Resumen del contenido

<2-4 frases de qué trata la fuente>

## Información clave

<secciones con datos concretos, tablas, listas>

## Relevancia para el sistema de trasplantes

<por qué importa para este proyecto>

## Implicancias tecnológicas

<oportunidades / limitaciones / gaps detectados>

## Citas relevantes

> "<cita textual>"

## Observaciones

<dudas, contradicciones, datos a verificar>
```

### Reglas
- **Nombre del archivo**: `NN_palabra_clave.md` en kebab/snake_case (`NN` = número de orden, dos dígitos).
- **Hash de URL canónica** al principio para que sea trazable.
- **Citas textuales** siempre entre `> ""`.
- Si encontrás varias URLs sobre el mismo tema, **agrupalas en una sola fuente** si tratan del mismo concepto; si tratan de cosas distintas, separá.

---

## 3. Plantilla para `analisis/NN_propuesta.md`

Los análisis son **más libres** pero deberían incluir:

```markdown
# Análisis — <Título de la propuesta>

> Encuadre breve. ¿Es complemento de otro análisis? Mencionarlo con link.
>
> **Fecha:** YYYY-MM-DD

---

## 0. TL;DR

<tabla o párrafo de 5-10 líneas con veredicto>

## 1. Encuadre del problema

<qué problema concreto resuelve y cuál NO>

## 2. Capacidades / restricciones técnicas

<lo que la tecnología puede / no puede hacer hoy>

## 3. Arquitectura propuesta

<diagrama ASCII + descripción de componentes>

## 4. Análisis de seguridad

<tabla amenaza/probabilidad/impacto/mitigación>

## 5. Análisis legal

<normas aplicables + riesgos + mitigaciones>

## 6. Contactos / canales para avanzar

<tabla actor/para qué/canal/tiempo esperado>

## 7. Plan de implementación

<fases con plazos>

## 8. Costos / BOM (si aplica)

<USD en tablas, sin "varía mucho">

## 9. Riesgos de proyecto (no técnicos)

<tabla>

## 10. Métricas de éxito

<medibles>

## 11. Ficheros relacionados

<links a sources/ y analisis/ relevantes>
```

---

## 4. Cómo organizar `research.md`

Es el **documento maestro**. Debe poder leerse de corrido y dar contexto sin abrir otros archivos. Estructura recomendada:

1. Mapa institucional (diagrama del ecosistema).
2. Workflow operacional canónico.
3. Sistema central (SINTRA o equivalente).
4. Marco normativo.
5. Algoritmos relevantes.
6. Logística.
7. Estadísticas.
8. Cuellos de botella (priorizados).
9. Oportunidades tecnológicas (por madurez).
10. Arquitectura conceptual.
11. Hipótesis estratégicas.
12. Inconsistencias / datos a verificar.
13. Patrones detectados.
14. Roadmap de investigación pendiente.
15. Índice de fuentes.
16. Glosario (o referenciar `GLOSARIO.md`).

**Actualizalo cada vez que un análisis nuevo arroja un hallazgo transversal** — no esperar a "tener todo listo".

---

## 5. Cuándo escribir cada tipo de archivo

| Situación | Dónde escribís |
|---|---|
| Encontraste una URL nueva con info útil | `sources/NN_*.md` |
| Encontraste algo que ya está cubierto en una fuente existente | Actualizá la fuente existente |
| Estás evaluando una solución concreta (ej: "facial recognition", "drones") | `analisis/NN_*.md` |
| El hallazgo cambia la visión general del problema | `research.md` (sección apropiada) |
| Conociste una sigla nueva | `GLOSARIO.md` |
| Cambió el alcance del proyecto | `README.md` + `sources/00_desafio_*.md` si es nueva versión del enunciado |

---

## 6. Prompts probados para usar Claude / LLMs

### 6.1 — Reconnaissance (Fase 1)

```
Necesito explorar el tema X dentro del sistema argentino de trasplantes.
Hacé 4-6 búsquedas web en paralelo cubriendo distintos ángulos:
- Marco normativo argentino
- Implementación técnica actual
- Comparación con UNOS / Eurotransplant / NHS BT
- Casos concretos / prensa
- Limitaciones documentadas
- Innovaciones en curso

Devolveme las URLs más relevantes con un resumen breve por cada una.
NO escribas archivos todavía — solo la lista priorizada.
```

### 6.2 — Deep dive (Fase 2)

```
Para cada una de estas URLs, hacé WebFetch y extraé:
- Datos cuantitativos
- Estructura técnica / arquitectura
- Actores citados
- Limitaciones explícitas
- Citas literales útiles

Después, escribí un archivo en sources/ siguiendo la plantilla del WORKFLOW.md.
```

### 6.3 — Synthesis (Fase 3)

```
Leé los archivos en sources/ y actualizá research.md.
- Detectá patrones (ej: "tres países usan X estrategia")
- Detectá contradicciones (ej: "fuente A dice Y, fuente B dice no-Y")
- Refiná las hipótesis estratégicas
- Marcá datos a verificar
NO repitas información que ya está en una fuente — research.md es síntesis, no copia.
```

### 6.4 — Critical analysis (Fase 4)

```
Quiero un análisis crítico de la siguiente propuesta: <descripción>.
Seguí la plantilla de analisis/ del WORKFLOW.md.
Sé explícito sobre:
- Qué resuelve y qué NO resuelve
- Por qué es viable / por qué no
- Costos en USD con sources
- Marco legal aplicable y riesgos
- Plan realista de 6-18 meses
NO inventes precios — buscalos en la web.
```

### 6.5 — Mejora de claridad

```
Releé el archivo X y mejorá:
- Reduce repeticiones entre secciones
- Asegurate que cada tabla tenga título y sea legible
- Verificá que cada afirmación tenga fuente
- Si hay datos sin verificar, marcalos como "estimación" o "a verificar"
NO cambies el formato si ya cumple la plantilla.
```

---

## 7. Reglas de calidad antes de "dar por cerrado" un archivo

Checklist mental:
- [ ] ¿Tiene URL canónica de la fuente?
- [ ] ¿Fecha de consulta?
- [ ] ¿Citas textuales separadas de paráfrasis?
- [ ] ¿Tablas en lugar de prosa larga cuando aplica?
- [ ] ¿Datos cuantitativos con número, unidad y año?
- [ ] ¿Implicancias tecnológicas accionables?
- [ ] ¿Observaciones / dudas registradas?
- [ ] ¿Vinculado a otros archivos relacionados con `[link](path)`?
- [ ] Si es análisis: ¿tiene plan, costos, plazos, riesgos?

---

## 8. Workflow de comandos (terminal / VS Code)

### Crear nueva fuente
```bash
# El número siguiente lo elegís según el último archivo en sources/
NEW=sources/19_nombre_corto.md
# Copiá la plantilla de WORKFLOW.md sección 2
# Editá en VS Code
code $NEW
```

### Buscar texto en todas las fuentes
Usá Grep en VS Code (Ctrl+Shift+F) o:
```bash
grep -rin "FHIR" sources/ analisis/ research.md
```

### Ver qué archivos tocaste hoy
```bash
ls -lt sources/ | head
ls -lt analisis/ | head
```

### Cuando quieras commit (cuando esté listo)
```bash
git init  # solo la primera vez si no es repo aún
git add README.md WORKFLOW.md research.md GLOSARIO.md sources/ analisis/
git status
git commit -m "research: <descripción concreta del avance>"
```

---

## 9. Vectores de investigación pendientes

Estos son los ángulos del desafío que **todavía no se atacaron a fondo**. Cada uno merece la secuencia completa (búsquedas → fuentes → análisis):

1. **Alertas Glasgow ≤7 desde HIS hospitalario**
   - ¿Qué HIS argentinos tienen webhooks?
   - HSI (Provincia BA), SISA, sistemas privados.
   - FHIR Subscription mechanism.
   - Pilotos hospitalarios existentes.

2. **Notificación realtime CUCAI/INCUCAI**
   - Stack actual de notificación (teléfono, mail, WhatsApp).
   - Twilio / Resend / FCM como alternativas técnicas.
   - SLA esperables.

3. **Capacitación / microlearning Ley Justina**
   - Estado del Programa Federal de Procuración (capacitación INCUCAI).
   - LMS opensource (Moodle, Open edX).
   - VR/AR ya explorada por Casa Justina (EducaciónX7).

4. **Dashboard cumplimiento institucional**
   - Indicadores del Hospital Donante Program.
   - Programa de Garantía de Calidad subprograma.
   - Auditoría comparativa entre hospitales.

5. **Modelo de financiamiento / sustentabilidad**
   - Quién paga el dispositivo (hospital, INCUCAI, ONG, financiador).
   - Modelo SaaS hardware (alquiler) vs venta.
   - Subsidios o créditos.

6. **Integración con SINTRA (camino concreto)**
   - Convenios actuales de SINTRA con terceros.
   - Caso de uso "extender SINTRA por la nube".
   - Antecedente "Calipso" ERP del INCUCAI.

7. **Protocolos hospitalarios desactualizados**
   - Estado real (encuesta a 3-5 hospitales).
   - Modelo a copiar (Modelo Español hospital-by-hospital).

---

## 10. Cuándo parar y consolidar

La tentación es seguir investigando para siempre. Frenos sugeridos:

- **Una semana sin un hallazgo realmente nuevo** → consolidá lo que tenés.
- **Más de 25 fuentes y no podés explicar el problema en 2 minutos** → falta síntesis, no fuentes.
- **Antes de cada reunión externa** (Casa Justina, INCUCAI, etc.) → preparar un documento de 1 página derivado del repo.
- **Antes de cualquier hito AFIDEER** → release del README + roadmap actualizado.

---

## 11. Anti-patrones a evitar

- ❌ **Hacer una fuente sin URL canónica** — no es fuente, es opinión.
- ❌ **Mezclar varios temas en un solo archivo de fuente** — hace imposible referenciarlo.
- ❌ **Análisis sin plan y sin costos** — no sirve para presentar.
- ❌ **Copiar texto de la web sin parafrasear** — es plagio + ilegible.
- ❌ **Datos sin año** ("España alcanzó X DPMH") — los datos envejecen.
- ❌ **Tablas sin título o con columnas ambiguas**.
- ❌ **"Etc." al final de listas importantes** — terminá la enumeración o decí qué falta.
- ❌ **Promesas sin métricas** ("mejora significativa", "ahorra mucho tiempo") — siempre cuantificar.

---

## 12. Resumen del workflow en 1 párrafo

> Investigá un ángulo del problema con búsquedas web amplias → leé cada fuente útil en profundidad → documentala en `sources/NN_*.md` con la plantilla → sintetizá los patrones transversales en `research.md` → cuando tengas una propuesta concreta, evaluala en `analisis/NN_*.md` con plan, costos y plazos → actualizá README e iterá. Cada commit del repo debería poder leerse como una historia coherente del avance del equipo.
