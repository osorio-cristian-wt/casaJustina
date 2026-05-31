# Res. 716/2019 — Protocolo Nacional de certificación de muerte encefálica (el "momento" que dispara la etapa 2)

- **URL:** https://www.argentina.gob.ar/normativa/nacional/resoluci%C3%B3n-716-2019-322558
- **URL secundaria:** https://www.argentina.gob.ar/sites/default/files/resolucion_716_2019_anexo_i.pdf (Anexo I — protocolo)
- **URL secundaria:** https://www.boletinoficial.gob.ar/detalleAviso/primera/206448/20190429 (Boletín Oficial)
- **URL secundaria:** https://www.incucai.gov.ar/index.php/profesionales/pasos-operativos/12-profesionales/128-certificacion-de-muerte
- **Fecha de consulta:** 2026-05-31
- **Tipo:** norma (Resolución Ministerial + protocolo INCUCAI)

## Resumen del contenido

La Res. 716/2019 aprueba el **"Protocolo Nacional para la Determinación del Cese Irreversible de las Funciones Encefálicas (Certificación del Fallecimiento)"**. Es la norma que define **el evento exacto que el proyecto quiere usar como disparador de la etapa 2**: el instante en que se certifica la muerte encefálica. Reemplazó la Res. 275/2010 y unifica el criterio a nivel nacional.

## Información clave

- **Qué es:** protocolo nacional, único y obligatorio, para diagnosticar el cese irreversible de las funciones encefálicas = certificación legal del fallecimiento.
- **Reemplaza** la Resolución N° 275/2010 del ex Ministerio de Salud.
- **Estructura del protocolo:** criterios de inclusión, períodos de evaluación/observación, métodos auxiliares (instrumentales) y consideraciones para situaciones especiales (incluye un protocolo pediátrico).
- **Confirmación instrumental obligatoria:** "es obligatorio corroborar la inactividad encefálica por medios técnicos o instrumentales para completar el diagnóstico de muerte encefálica" (p. ej. EEG, Doppler transcraneal, etc.).
- **Es un acto médico humano y reglado:** lo realiza personal médico siguiendo el protocolo; **no es automatizable** — es precisamente el punto donde la máquina NO decide.
- Se articula con la Ley 27.447 (Art. 36/37 definen la muerte; Art. 39 obliga a iniciar el proceso de donación al certificar; ver [04](04_ley_27447_justina.md) y [26](26_marco_legal_deteccion_premortem.md)).

## Relevancia para el sistema de trasplantes

Es **la frontera legal y operativa entre la etapa 1 (detección pre-mortem) y la etapa 2 (logística de procuración)**. Hasta este momento el paciente está vivo y los datos se manejan dentro del hospital ([26](26_marco_legal_deteccion_premortem.md)). **Al firmarse esta certificación**, dos cosas cambian de golpe: (1) opera la presunción de donación de la Ley Justina ([28](28_ley_27447_art33_consentimiento_familiar.md)) y (2) el Art. 39 obliga a iniciar el proceso. Ese es exactamente el evento que el sistema debe detectar para **disparar avisos y logística**.

## Implicancias tecnológicas

- **El disparador de la etapa 2 es la certificación registrada en el HIS**, no una inferencia automática. El sistema escucha "se cargó el acta/diagnóstico de muerte encefálica" (un `Procedure`/`Observation`/`Condition` en FHIR), no lo deduce.
- **Diseño honesto:** la herramienta **no** declara la muerte ni la sugiere. Espera a que el médico la certifique según Res. 716/19 y recién entonces actúa sobre la información y la coordinación.
- **Dato a modelar:** definir cómo se representa la certificación de muerte encefálica en los HIS argentinos (¿campo estructurado? ¿documento adjunto?) para poder detectarla — **a verificar**.
- Articula con la detección previa: el caso que disparó la alerta PSG<7 ([19](19_incucai_deteccion_psg7_garantia_calidad.md)) puede evolucionar a esta certificación, cerrando el ciclo detección→procuración.

## Citas relevantes

> "Protocolo Nacional para la Determinación del Cese Irreversible de las Funciones Encefálicas (Certificación del Fallecimiento)." (Res. 716/2019, título del Anexo I)

> "En Argentina es obligatorio corroborar la inactividad encefálica por medios técnicos o instrumentales para completar el diagnóstico de muerte encefálica." (resumen normativo)

## Observaciones

- **A verificar (importante, fetch del PDF falló por formato):** número exacto de profesionales que deben firmar, especialidades requeridas, duración del período de observación y métodos instrumentales aceptados. Pedir el texto del Anexo I de la Res. 716/19 o la página del INCUCAI "Certificación de Muerte".
- **A verificar:** existe un protocolo pediátrico específico dentro de la 716/19 (la donación pediátrica tiene reglas propias — la familia/representantes sí deciden para menores).
- Relación con el workflow de 10 etapas: research.md §2, etapa 2 (certificación) y etapa 5 (mantenimiento del donante).
