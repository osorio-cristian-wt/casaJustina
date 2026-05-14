# Pasos operativos del proceso de trasplante

- **URL:** https://www.argentina.gob.ar/salud/incucai/comunidad-hospitalaria/pasos-operativos
- **Fecha de consulta:** 2026-05-14
- **Tipo:** Documentación institucional oficial — workflow operacional

## Resumen del contenido

Define los 10 pasos del workflow de procuración y trasplante desde la detección del potencial donante hasta el acondicionamiento del cuerpo, con el actor responsable y la base normativa en cada uno.

## Información clave: Workflow detallado

### Etapa 1 — Detección del potencial donante
- **Actor:** Médicos de UTI / Unidades de Cuidados Intensivos.
- **Trigger:** Signos clínicos de muerte encefálica o paro cardíaco.
- **Tecnología:** Sistemas de monitoreo en UTI.
- **Punto crítico:** La detección temprana es **el factor más determinante** del éxito posterior. Subnotificación de potenciales donantes = pérdida de la cadena entera.

### Etapa 2 — Certificación de muerte encefálica
- **Actor:** Equipo médico (no procurador).
- **Base legal:** Cese irreversible de funciones circulatorias o encefálicas.
- **Norma:** Resolución 716/19 (protocolo nacional).
- **Decisión:** Determina si se procuran órganos o sólo tejidos.

### Etapa 3 — Comunicación con la familia
- **Actor:** Equipo tratante → equipo de procuración.
- **Estructura:** 2 etapas — notificación de fallecimiento → posibilidad de donación.
- **Consulta:** Registro nacional de manifestación de voluntad + DNI.
- **Menores:** Padres/tutores autorizan.

### Etapa 4 — Evaluación y selección
- **Actor:** Equipo médico evaluador.
- **Componentes:** Historia clínica, estudios hemodinámicos, laboratorios, serologías.
- **Reglas órgano-específicas:** Ej. Hepatitis C excluye hígado pero no riñones.
- **Norma:** Guías "Procurar para Curar".

### Etapa 5 — Tratamiento del donante (mantenimiento)
- **Actor:** UTI.
- **Objetivo:** Soporte artificial multi-órgano (perfusión + oxigenación).
- **Tiempo crítico:** Cada hora cuenta — evitar paro cardíaco.

### Etapa 6 — Intervención judicial (si aplica)
- **Trigger:** Muerte violenta, traumática o de causa no esclarecida.
- **Decisión:** Magistrado autoriza ablación.

### Etapa 7 — Distribución y asignación
- **Actor:** INCUCAI nacional / CUCAI provincial.
- **Tecnología:** **SINTRA** genera lista ordenada de receptores potenciales.
- **Proceso:** Oferta secuencial por la lista hasta que un equipo acepta.
- **Variables:** Compatibilidad, urgencia, antigüedad, regionalidad.

### Etapa 8 — Ablación y acondicionamiento
- **Actor:** Múltiples equipos quirúrgicos (uno por órgano) trabajando en paralelo.
- **Lugar:** Quirófano del hospital donde falleció el donante.
- **Tecnología:** Contenedores a 4°C con soluciones de preservación.
- **Tiempo crítico:** Isquemia fría típica <20 horas.

### Etapa 9 — Transporte
- **Actor:** Personal de procuración (riñones/córneas) o equipo de trasplante (otros órganos).
- **Tecnología:** Contenedores con control térmico.
- **Ruta:** Hospital procurador → centro de trasplante.

### Etapa 10 — Acondicionamiento del cuerpo
- **Actor:** Equipo de procuración con protocolos de bioseguridad.
- **Final:** Entrega a familia/autoridad judicial.

## Decisiones automatizables vs humanas

| Decisión | Automatizable | Hoy es humana |
|---|---|---|
| Match HLA/sanguíneo | Sí (algoritmo determinista) | Parcialmente SINTRA |
| Score MELD/PELD | Sí (fórmula) | Calculada en SINTRA |
| Score renal (puntaje) | Sí | SINTRA |
| Aceptación del órgano por equipo | **No — decisión médica** | Humana |
| Ruta logística óptima | Sí (algoritmo) | Coordinación telefónica |
| Detección de potencial donante | Parcial (alertas Glasgow ≤7) | Mayormente humana |
| Notificación familia | No | Humana |
| Autorización judicial | No | Humana |

## Relevancia para el sistema de trasplantes

Este es el **mapa canónico del workflow**. Cualquier modernización debe respetar estos 10 hitos como mínimos legales y operativos.

## Implicancias tecnológicas

- **Etapa 1 (detección)** — Glasgow ≤7 es el trigger del subprograma de calidad. Es **el evento más subnotificado** y donde hay mayor oportunidad de automatización (alertas desde HIS hospitalario).
- **Etapa 7 (asignación)** — SINTRA genera la lista pero la **comunicación de oferta a cada equipo es manual** (llamada telefónica / correo). Oportunidad: notificaciones en tiempo real con SLA medible.
- **Etapa 8–9** — Coordinación entre equipos quirúrgicos múltiples en paralelo + transporte. **Falta orquestador con timeline compartido y eventos**.

## Citas relevantes

> "Procurar para Curar" — guías que determinan viabilidad de órganos por patología.

> "El órgano se ofrece secuencialmente hacia abajo de la lista hasta su aceptación."

## Observaciones

- El workflow está bien definido legalmente pero **no se observa un timeline compartido en tiempo real** entre actores.
- La oferta secuencial es costosa en tiempo cuando hay rechazos: cada "no" agrega minutos a la isquemia.
- La doble cadena (médico procurador / médico trasplantista) genera duplicación de evaluaciones.
