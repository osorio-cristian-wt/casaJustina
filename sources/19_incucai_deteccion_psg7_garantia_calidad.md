# Detección de potenciales donantes en Argentina — Subprograma de Garantía de Calidad (PSG<7)

- **URL:** https://www.argentina.gob.ar/salud/incucai/calidad-en-procuracion
- **URL secundaria:** https://www.argentina.gob.ar/salud/incucai/comunidad-hospitalaria/programa-federal-de-procuracion
- **URL secundaria:** https://www.argentina.gob.ar/salud/incucai/comunidad-hospitalaria/hospital-donante
- **URL secundaria:** https://cudaio.gob.ar/sistema-nacional-de-procuracion-2/
- **Fecha de consulta:** 2026-05-31
- **Tipo:** oficial (INCUCAI / Ministerio de Salud / CUDAIO)

## Resumen del contenido

El INCUCAI estructura la detección de donantes alrededor de tres dispositivos: el **Programa Federal de Procuración** (2003, crea la figura del coordinador hospitalario), el **Programa Hospital Donante** (instala la procuración como actividad rutinaria del hospital, no de expertos) y el **Subprograma de Garantía de Calidad**, que se basa explícitamente en **la detección y monitoreo de pacientes neurocríticos con Glasgow ≤7 (PSG<7)**. Es decir: el criterio que internacionalmente dispara la búsqueda de donantes (Glasgow bajo) **ya está adoptado en la normativa argentina** — pero el monitoreo es manual.

## Información clave

| Dispositivo INCUCAI | Qué hace | Año/Norma |
|---|---|---|
| **Programa Federal de Procuración** | Descentraliza la procuración; crea la figura del **coordinador hospitalario** como motor de generación de donantes intrahospitalarios. | 2003 |
| **Programa Hospital Donante** | Entiende la procuración "no como actividad de expertos, sino como responsabilidad del hospital en su conjunto", actividad habitual y rutinaria. | — |
| **Subprograma de Garantía de Calidad** | Se articula con el Federal de Procuración y se basa en la **detección y monitoreo de pacientes neurocríticos con Glasgow ≤7 (PSG<7)**. | Res. 199/04 (marco) |
| **Perfil del Coordinador Hospitalario** | Define funciones del coordinador. | Res. 1642/22 (Anexo II) |

- **El criterio de detección argentino es PSG<7** (Puntaje de Glasgow < 7), no el ≤4/≤5 de otros sistemas — coincide con lo ya registrado en el `GLOSARIO.md`.
- La **detección** se define oficialmente como "la **identificación oportuna** del paciente que evoluciona con síndrome clínico de muerte encefálica o a la muerte por cese irreversible de las funciones circulatorias".
- "La función del coordinador hospitalario **comienza con la detección del potencial donante** y termina con la donación efectiva de órganos para trasplante."
- El monitoreo de pacientes PSG<7 es la herramienta de auditoría del subprograma: permite saber cuántos potenciales donantes hubo vs. cuántos se detectaron (medición de **donantes escapados / muertes encefálicas no detectadas**).

## Relevancia para el sistema de trasplantes

**Máxima.** Esta es la fuente que cierra el caso del proyecto: el sistema argentino **ya define el disparador clínico** (Glasgow ≤7) y **ya tiene la figura** (coordinador hospitalario) que debe actuar sobre él. Lo que falta no es la norma ni el rol, sino la **ejecución técnica**: hoy ese monitoreo PSG<7 depende de que alguien lea fichas o planillas. Automatizar la detección de PSG<7 desde el HIS no inventa un proceso nuevo — **digitaliza un proceso que la normativa ya exige**. Eso reduce drásticamente el riesgo regulatorio y político del proyecto.

## Implicancias tecnológicas

- **El "trigger" del producto está pre-validado por norma:** alertar cuando un paciente registra Glasgow ≤7 es exactamente lo que el Subprograma de Garantía de Calidad pide monitorear. No hay que convencer al INCUCAI del criterio; hay que ofrecerle la herramienta.
- **Destinatario natural de la alerta = coordinador hospitalario** (no el CUCAI directamente, al menos en la detección pre-mortem). Esto alinea con la idea del proyecto: avisar a la persona encargada de donantes para que "esté atenta a ese paciente".
- **Métrica de éxito ya existe:** el propio subprograma mide tasa de detección. Un piloto puede reportar mejora en esa métrica con números comparables.
- **Gap operativo:** la detección manual depende de carga de trabajo y memoria del personal de UTI — exactamente el punto que la evidencia internacional muestra que la automatización resuelve (ver [21_automatizacion_referral_ehr_estudio.md](21_automatizacion_referral_ehr_estudio.md)).

## Citas relevantes

> "El Subprograma de Garantía de Calidad en el proceso de procuración de órganos se basa en la detección y monitoreo de Pacientes Neurocríticos con Glasgow igual o menor a 7 (PSG<7)." (INCUCAI — Calidad en procuración)

> "La procuración de órganos y tejidos no es una actividad de expertos, sino una responsabilidad del hospital en su conjunto." (INCUCAI — Programa Hospital Donante, paráfrasis del enfoque)

> "La función del coordinador hospitalario comienza con la detección del potencial donante." (INCUCAI — Programa Federal de Procuración)

## Observaciones

- **A verificar:** el detalle fino del Subprograma de Garantía de Calidad (frecuencia de registro de Glasgow exigida, formato del reporte, si hay un instrumento nacional estandarizado) no está en la página pública; requiere los anexos de la Res. 199/04 y/o pedido formal al INCUCAI vía Casa Justina.
- **A verificar:** si algún CUCAI provincial ya digitalizó el monitoreo PSG<7 (CUCAIBA, CUDAIO) — sería antecedente directo o "competencia".
- Marco legal del deber de detección: [26_marco_legal_deteccion_premortem.md](26_marco_legal_deteccion_premortem.md) (Ley 27.447 Art. 14).
- Relación con cuello de botella #1 (subdetección): [14_cuellos_botella_operacionales.md](14_cuellos_botella_operacionales.md).
