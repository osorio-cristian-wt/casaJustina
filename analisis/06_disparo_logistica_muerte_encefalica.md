# Análisis — Auto-disparo de logística al certificarse la muerte encefálica (etapa 2)

> Segunda etapa del sistema de detección/notificación. Continúa a [05_alertas_glasgow_his_hospitalarios.md](05_alertas_glasgow_his_hospitalarios.md) (etapa 1, detección pre-mortem).
>
> Encuadre del equipo: *"en el momento en que se declara la muerte encefálica, que se disparen todas las alarmas — que el médico sepa que no tiene que desconectarlo, y que el coordinador de trasplantes y toda la logística sean avisados."*
>
> **Fecha:** 2026-05-31

---

## 0. TL;DR

| Pregunta | Veredicto |
|---|---|
| ¿Es legal disparar la logística al certificarse la muerte? | **Sí.** Al certificarse la muerte encefálica (Res. 716/2019) opera la **presunción de donación** de la Ley Justina; el Art. 33 establece que **no hace falta consentimiento familiar para adultos** ([sources/04](../sources/04_ley_27447_justina.md)) y el Art. 39 ("Notificación") **obliga a iniciar el proceso**. |
| ¿"No desconectar" es real? | **Sí, y es crítico.** Tras la muerte encefálica el objetivo cambia a mantener la perfusión de órganos; **hasta el 20 % se pierde por mal manejo del donante** ([sources/30](../sources/30_manejo_donante_muerte_encefalica_no_desconectar.md)). |
| ¿Hay evidencia de impacto? | **Sí, contundente.** Un CDS automatizado bajó el tiempo de notificación de **30,2 h → 1,7 h** y aumentó donantes, con pocos falsos positivos ([sources/31](../sources/31_cds_muerte_encefalica_inminente_zier.md)). USA lo exige por ley: avisar **≤1 h y antes de retirar soporte vital** ([sources/29](../sources/29_required_referral_cms_42cfr48245.md)). |
| ¿Qué se puede automatizar y qué no? | **Automatizar:** notificación (coordinador + CUCAI + equipos + transporte), armado del expediente, "modo mantenimiento del donante" y el reloj del operativo. **NO automatizar:** la certificación médica de la muerte y la comunicación con la familia (siguen siendo humanas). |
| **Recomendación única** | **Extender el adaptador "DonorTrigger" con un segundo disparador: la certificación de muerte encefálica.** Implementarlo como **confirmación humana de un toque (Arquitectura B)** en la app del coordinador —porque certificar es un acto humano—, que dispara la **cascada de notificación + modo mantenimiento + reloj**. Auto-detección desde el HIS (Arq. A) como mejora posterior. MVP demostrable para AFIDEER. |

**Idea en una frase:** el mismo sistema que en la etapa 1 vigila al paciente, en la etapa 2 —cuando el médico certifica la muerte— **larga en segundos todas las alarmas y le recuerda al equipo que debe mantener al donante, no desconectarlo**, mientras el coordinador y la logística ya fueron avisados.

---

## 1. Encuadre del problema

### Qué resuelve
La **demora y dispersión de la notificación** cuando ya hay un donante real, y el **riesgo de perder los órganos** por desconexión o mal manejo. Hoy, al certificarse la muerte, la activación del operativo depende de llamados telefónicos en cascada (cuello #3, [sources/14](../sources/14_cuellos_botella_operacionales.md)) y de que el equipo de UTI cambie a tiempo el objetivo terapéutico.

### Qué NO resuelve / límites explícitos
- **No certifica ni sugiere la muerte.** Espera el acto médico reglado (Res. 716/2019, [sources/27](../sources/27_resolucion_716_2019_muerte_encefalica.md)). La máquina **nunca** decide la muerte.
- **No reemplaza la conversación con la familia** (informar/acompañar/verificar voluntad — [sources/04](../sources/04_ley_27447_justina.md), Art. 33). No la automatiza.
- **No hace el manejo clínico del donante** — lo asiste mostrando metas (DMG) y conectando al coordinador.
- **No mueve el órgano ni asigna receptores** — eso es SINTRA (etapa 7) y la coordinación logística (otros vectores).

### Las dos etapas, encadenadas
```
ETAPA 1 (analisis/05)                 ETAPA 2 (este análisis)
Glasgow ≤7 pre-mortem                  Certificación de muerte encefálica
   │ detectar + vigilar                   │ disparar logística + mantener donante
   ▼ (dentro del hospital)                ▼ (se habilita circuito externo)
coordinador hospitalario              coordinador + CUCAI + equipos + transporte
```
La **certificación de muerte encefálica** es la bisagra: cambia el marco legal (de paciente vivo protegido a donante presunto) y el objetivo clínico (de salvar al paciente a preservar órganos).

---

## 2. Capacidades / restricciones técnicas

### Lo que SÍ se puede hoy
- **Detectar el evento de certificación** si el HIS lo registra como dato estructurado (`Procedure`/`Observation`/`Condition` FHIR) — o capturarlo por **confirmación humana** en la app.
- **Disparar una cascada de notificaciones** multi-destinatario en segundos (push/SMS/email/voz).
- **Mostrar metas de mantenimiento del donante (DMG)** usando datos que el monitor de UTI ya provee (FC, PAM, diuresis, temperatura — [sources/30](../sources/30_manejo_donante_muerte_encefalica_no_desconectar.md), [sources/23](../sources/23_monitores_uti_captura_datos.md)).
- **Iniciar el "reloj" del operativo** y un timeline compartido (research.md §9, §10).

### Restricciones reales
- **Certificar es humano** → el disparo más confiable es **semi-automático** (un humano confirma), no una inferencia de la máquina.
- **¿Está el evento de muerte encefálica estructurado en el HIS argentino?** Probablemente no de forma uniforme → **a verificar**; refuerza la opción de confirmación humana.
- **La cascada externa (CUCAI/INCUCAI) toca datos identificables post-mortem** → ahí ya es legal, pero requiere canal y acuerdo (la notificación realtime CUCAI/INCUCAI es el vector #2 del backlog; este análisis se apoya en él).
- **SINTRA no tiene API** → la integración con el circuito nacional sigue siendo el límite conocido (research.md §3).

---

## 3. Arquitectura propuesta

### Núcleo: extender "DonorTrigger" con un segundo evento y un orquestador
El adaptador de la etapa 1 ya sabe escuchar el HIS y notificar. La etapa 2 agrega: **(1) captar el evento "muerte encefálica certificada", (2) ejecutar una cascada de notificación, (3) activar el "modo mantenimiento del donante", (4) arrancar el reloj del operativo.**

```
  Certificación muerte encefálica (Res. 716/19)  ── acto médico humano ──┐
                                                                          │
   (A) HIS registra Procedure/Condition  ─┐                               │
   (B) coordinador confirma 1-toque app ──┼──►  ORQUESTADOR "DonorTrigger E2"
   (C) híbrido: HIS pre-arma + humano OK ─┘            │
                                                       ├─► cascada notificación:
                                                       │     coordinador + CUCAI + equipos + transporte
                                                       ├─► "MODO MANTENIMIENTO DONANTE":
                                                       │     metas DMG en pantalla + aviso "NO desconectar"
                                                       ├─► armado de expediente del donante (precarga)
                                                       └─► arranca RELOJ del operativo (timeline + viabilidad)
```

### Tres alternativas para capturar el disparador

| Criterio | **A — Auto-detección HIS** | **B — Confirmación humana 1-toque** | **C — Híbrido** |
|---|---|---|---|
| Cómo se gatilla | El HIS emite evento estructurado de muerte encefálica (FHIR Subscription/polling) | El coordinador/médico marca "ME certificada" en la app | El HIS pre-arma el caso (vio el acta) y un humano confirma |
| Fiabilidad legal/clínica | Depende de la calidad del dato del HIS | **Alta** (un humano valida el acto humano) | Alta |
| Latencia | Segundos | Segundos (depende del humano) | Segundos |
| Requisito del HIS | Evento de ME estructurado (raro hoy) | **Ninguno** | Lectura del HIS |
| Riesgo de falso disparo | Medio (dato mal cargado) | **Bajo** | Bajo |
| Viable para demo AFIDEER | Difícil | **Sí, directo** | Parcial |
| Rol recomendado | Mejora futura | **MVP y producción inicial** | Objetivo de madurez |

**Decisión:** **empezar por B** (confirmación humana de un toque) — es lo más fiable, no depende de que el HIS modele la muerte encefálica, y respeta que certificar es un acto humano. Migrar a **C/A** cuando el HIS objetivo exponga el evento estructurado. El valor no está en "adivinar" la muerte, sino en que **un solo toque dispare en segundos lo que hoy lleva horas de llamados**.

### Componentes nuevos respecto de la etapa 1
1. **Orquestador de cascada** (event-driven; encaja con el "Workflow Orchestrator / Temporal.io" de research.md §10). Reintentos, confirmaciones de recepción, escalamiento si nadie responde.
2. **"Modo mantenimiento del donante":** pantalla con metas DMG en vivo + alerta destacada **"donante en muerte encefálica: mantener soporte, NO retirar"**, y salvaguarda si se registra una orden incompatible ([sources/30](../sources/30_manejo_donante_muerte_encefalica_no_desconectar.md), [sources/29](../sources/29_required_referral_cms_42cfr48245.md)).
3. **Expediente precargado del donante** (datos clínicos, serologías, estudios) para acelerar la evaluación.
4. **Reloj del operativo** que se conecta con la cinética de viabilidad ([analisis/04](04_modelo_dinamico_viabilidad_cardiaca.md)).

> La **mecánica de notificación realtime** (Twilio/voz/SMS/FCM, SLAs, integración CUCAI/INCUCAI) es el **vector #2 del backlog** (research.md §9). Este análisis define *qué* se dispara y *cuándo*; el *cómo* del transporte de la notificación se profundiza ahí.

---

## 4. Análisis de seguridad

| Amenaza | Prob. | Impacto | Mitigación |
|---|---|---|---|
| Falso disparo de operativo (notificar sin muerte real) | Baja (con Arq. B) | Alto | Confirmación humana; doble check para la cascada externa; log inmutable. |
| Datos identificables del donante al circuito externo | Media | Alto | Post-mortem ya es lícito (presunción); igual cifrar, mTLS, minimizar, auditar. |
| Cascada no llega (caída/SLA) | Media | Alto | Reintentos, multicanal (push+SMS+voz), escalamiento, **el proceso manual sigue vigente como fallback**. |
| Orden de desconexión pese a ser donante | Media | **Alto** | Salvaguarda que alerta si hay WLST/extubación tras certificación; mensaje "no desconectar" destacado. |
| Manipulación del reloj/expediente | Baja | Medio | Ledger append-only (trazabilidad, Art. 57 inc. 1 Ley 27.447), control de cambios. |
| Sobre-notificación a equipos (fatiga) | Media | Medio | Cascada segmentada por rol; sólo lo accionable a cada actor ([sources/25](../sources/25_alert_fatigue_cds_uti.md)). |

**Principio rector (igual que etapa 1):** el sistema es **aditivo** — acelera y asegura el aviso; nunca bloquea ni sustituye el acto médico ni el proceso manual.

---

## 5. Análisis legal

| Norma | Qué habilita / exige | Cómo cumplir |
|---|---|---|
| **Res. 716/2019** | Define el acto de certificación de muerte encefálica (humano, instrumental obligatorio). | El sistema **espera** la certificación; no la infiere. |
| **Ley 27.447 Art. 33** | Donante presunto; **sin consentimiento familiar para adultos** (sí para menores). | El disparo de logística se ampara en la presunción; ramificar flujo para menores. |
| **Ley 27.447 Art. 39** | Al certificar la muerte, **debe iniciarse el proceso** de donación. | Automatizar el inicio = ayudar a cumplir el deber, no crear obligación nueva. |
| **Ley 27.447 Art. 14** | Deber de detección/evaluación/tratamiento del donante. | "Modo mantenimiento del donante" operacionaliza el deber. |
| **CMS 42 CFR 482.45 (USA, benchmark)** | Avisar al OPO ≤1 h y **antes de retirar soporte vital**. | Adoptar el SLA ≤1 h y la salvaguarda "avisar antes de desconectar" como meta de diseño ([sources/29](../sources/29_required_referral_cms_42cfr48245.md)). |
| **Ley 26.529 / 25.326** | Confidencialidad / datos sensibles. | Acceso por rol, secreto, auditoría; minimización en notificaciones. |

**Diferencia clave con la etapa 1:** acá el paciente **ya falleció** (certificado) → opera la presunción y **es lícito** notificar al circuito externo con datos del donante. La etapa 1 debía quedarse dentro del hospital; la etapa 2 ya puede salir.

**Corrección documental:** este hallazgo corrige una imprecisión de [05](05_alertas_glasgow_his_hospitalarios.md) §1 ("la ley exige hacerlo con la familia"): la ley **no exige consentimiento familiar para adultos**; la comunicación familiar es buena práctica + verificación de voluntad, no autorización.

---

## 6. Contactos / canales para avanzar

| Actor | Para qué | Canal | Tiempo |
|---|---|---|---|
| **Casa Justina** | Sponsor; valida flujo con coordinadores | https://www.casajustina.org | Inmediato |
| **Coordinador hospitalario** | Validar la cascada y el "modo mantenimiento"; definir destinatarios | Vía Casa Justina / UAP | Semanas |
| **INCUCAI — Procuración** | Confirmar circuito de notificación post-certificación + interés en "required referral a la argentina" | Nota formal vía Casa Justina | 1-3 meses |
| **CUCAI Entre Ríos** | Destinatario provincial de la cascada (cercanía UAP) | cucai@entrerios.gov.ar (verificar) | 1-3 meses |
| **Intensivistas / SATI** | Validar metas DMG y mensaje "no desconectar" | Vía UAP Cs. de la Salud / SATI | 1-2 meses |
| **AAIP** | Datos del donante en la cascada externa | https://www.argentina.gob.ar/aaip | 2-4 meses |

---

## 7. Plan de implementación

| Fase | Plazo | Entregable | Dependencia |
|---|---|---|---|
| **F0 — Validación** | Mes 0-1 | Mapear con 2-3 coordinadores: ¿a quién se avisa hoy, en qué orden, cuánto tarda? | Casa Justina |
| **F1 — MVP demo (AFIDEER)** | Mes 1-3 | App con botón "ME certificada" (Arq. B) → cascada simulada a coordinador+CUCAI+equipos + pantalla "modo mantenimiento DMG" + reloj | Ninguna externa (simulado) |
| **F2 — Cascada real multicanal** | Mes 3-5 | Notificación real (push/SMS/voz) con reintentos, confirmación y escalamiento; medición de tiempos | Stack de notificación (vector #2) |
| **F3 — Piloto 1 hospital** | Mes 5-9 | Integración con un efector real; medir tiempo a notificación vs. baseline; DPIA | Convenio + HIS + legal |
| **F4 — Auto-detección + escala** | Mes 9-18 | Captura del evento estructurado de ME (Arq. A/C) donde el HIS lo permita; escala a 5 hospitales | HIS con evento estructurado |

**Camino crítico:** igual que la etapa 1, no es el software — es el **convenio con un efector** y la **definición del circuito de notificación con el CUCAI**. F1 es 100 % simulable → no bloquea AFIDEER.

---

## 8. Costos / BOM

Software-first, igual que [05](05_alertas_glasgow_his_hospitalarios.md). Costo incremental sobre el adaptador de la etapa 1 (mucho se reutiliza).

### Tier A — Demo AFIDEER
| Concepto | USD |
|---|---|
| Reutiliza adaptador etapa 1 + orquestador (open source, p. ej. Temporal/NATS) | $0 |
| Notificaciones demo (FCM/email free tier) | $0 |
| Hosting | $0-20/mes |
| **Total** | **~$0-60** |

### Tier B — Piloto 1 hospital (12 meses)
| Concepto | USD |
|---|---|
| Notificaciones reales (Twilio voz+SMS, volumen bajo) | $200-800 |
| Orquestador productivo (hosting + ops) | $600-1.500 |
| Integración circuito CUCAI (definición + pruebas) | $1.000-3.000 |
| DPIA / legal (incremental) | $500-1.500 |
| Desarrollo / ajustes | pro bono - $4.000 |
| **Total** | **~$2.300-10.800** |

### Tier C — Producción 5 hospitales (anual)
| Concepto | USD/año |
|---|---|
| Notificaciones (volumen 5 efectores) | $1.000-3.000 |
| Orquestador multi-tenant + soporte | $4.000-8.000 |
| Integración CUCAI/INCUCAI (1 vez, amortizada) | $3.000-8.000 |
| Mantenimiento | $3.000-6.000 |
| **Total** | **~$11.000-25.000/año** |

**Valor:** bajar la notificación de **30 h a <2 h** ([sources/31](../sources/31_cds_muerte_encefalica_inminente_zier.md)) y evitar perder órganos por desconexión/mal manejo (hasta 20 %, [sources/30](../sources/30_manejo_donante_muerte_encefalica_no_desconectar.md)). Cada donante recuperado ≈ varios trasplantes; un solo caso paga el piloto.

---

## 9. Riesgos de proyecto (no técnicos)

| Riesgo | Prob. | Impacto | Mitigación |
|---|---|---|---|
| El circuito de notificación CUCAI no está definido para terceros | Alta | Alto | Empezar dentro del hospital (coordinador+equipos); sumar CUCAI vía convenio; apoyarse en vector #2. |
| Resistencia "esto lo decide el médico, no una app" | Media | Medio | Arq. B (humano confirma); el sistema asiste, no decide; mensaje claro de rol. |
| Percepción de "apurar la muerte"/ética | Media | **Alto** | Comunicar el desacople: el sistema actúa **después** de la certificación humana; nunca antes ni la sugiere. |
| Evento de ME no estructurado en el HIS | Alta | Medio | Arq. B no lo necesita; auto-detección queda para F4. |
| Dependencia del stack de notificación (vector #2) | Media | Medio | MVP con canal simple; multicanal en F2. |

---

## 10. Métricas de éxito

| Métrica | Baseline | Meta | Cómo se mide |
|---|---|---|---|
| **Tiempo de certificación → notificación al coordinador/CUCAI** | horas (manual) | **≤1 h** (benchmark CMS/Zier) | timestamp confirmación → notificación |
| **% de operativos con aviso ≤1 h** | a medir | >70 % (como Zier post-CDS) | log de cascada |
| **% de donantes con "modo mantenimiento" activo** | — | >90 % | uso del módulo DMG |
| **Órganos perdidos por mal manejo/desconexión** | a medir | reducir (objetivo: <20 %) | auditoría de operativos |
| **Cumplimiento de DMG** (parámetros en meta) | — | mejora vs. baseline | datos de monitor/HIS |
| **Tiempo total certificación → ablación** | a medir | reducir | timeline del operativo |

---

## 11. Ficheros relacionados

**Fuentes que sustentan este análisis:**
- [../sources/27_resolucion_716_2019_muerte_encefalica.md](../sources/27_resolucion_716_2019_muerte_encefalica.md) — el evento disparador (certificación)
- [../sources/04_ley_27447_justina.md](../sources/04_ley_27447_justina.md) — base legal (donante presunto Art. 33, notificación Art. 39)
- [../sources/29_required_referral_cms_42cfr48245.md](../sources/29_required_referral_cms_42cfr48245.md) — benchmark "avisar antes de desconectar" (USA)
- [../sources/30_manejo_donante_muerte_encefalica_no_desconectar.md](../sources/30_manejo_donante_muerte_encefalica_no_desconectar.md) — por qué no desconectar (DMG, −20 %)
- [../sources/31_cds_muerte_encefalica_inminente_zier.md](../sources/31_cds_muerte_encefalica_inminente_zier.md) — evidencia (30,2 h → 1,7 h)
- [../sources/14_cuellos_botella_operacionales.md](../sources/14_cuellos_botella_operacionales.md) — cuello #3 (notificación en cascada manual)

**Análisis relacionados:**
- [05_alertas_glasgow_his_hospitalarios.md](05_alertas_glasgow_his_hospitalarios.md) — etapa 1 (detección pre-mortem); este análisis la continúa
- [04_modelo_dinamico_viabilidad_cardiaca.md](04_modelo_dinamico_viabilidad_cardiaca.md) — el "reloj" de viabilidad que arranca con el mantenimiento del donante

**Síntesis:** research.md §8 ("Profundización de la etapa 2"), §9 (oportunidad A9), §13 (patrones 10-11).

**Próximo vector encadenado:** notificación realtime CUCAI/INCUCAI (stack técnico de la cascada) — WORKFLOW.md §9 vector #2.
