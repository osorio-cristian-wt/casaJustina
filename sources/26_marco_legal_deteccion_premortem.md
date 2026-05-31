# Marco legal de la detección pre-mortem — Ley 27.447, 26.529 y 25.326

- **URL:** https://www.argentina.gob.ar/sites/default/files/ley-27447.pdf (Ley 27.447 — Justina)
- **URL secundaria:** http://www.legisalud.gov.ar/pdf/ley26529_ta.pdf (Ley 26.529 — Derechos del Paciente)
- **URL secundaria:** https://servicios.infoleg.gob.ar/infolegInternet/anexos/60000-64999/64790/norma.htm (Ley 25.326 — Protección de Datos Personales)
- **Fecha de consulta:** 2026-05-31
- **Tipo:** norma (leyes nacionales argentinas)

## Resumen del contenido

Encuadre legal del punto más delicado de la idea: **identificar a un potencial donante mientras todavía está vivo** y avisar a un referente. Hay tres leyes en juego. La conclusión: la **detección pre-mortem está habilitada e incluso es un deber del establecimiento** (Ley 27.447 Art. 14), pero como el paciente está vivo, **la presunción de donación todavía NO aplica** y los datos siguen protegidos por las leyes de paciente (26.529) y de datos personales (25.326). El diseño debe minimizar datos.

## Información clave

### Ley 27.447 (Justina) — habilita y obliga a detectar

| Artículo | Qué dice | Implicancia para el proyecto |
|---|---|---|
| **Art. 14** | Los establecimientos que reúnan las características que fije la reglamentación deben contar con **servicios destinados a la donación que permitan la correcta detección, evaluación y tratamiento del donante**. | **Deber legal de detección** → la herramienta ayuda a cumplir una obligación existente, no crea una práctica nueva. |
| **Art. 36/37** | La muerte se certifica por cese irreversible de funciones **circulatorias o encefálicas**, con examen y período de observación según protocolo (Res. 716/19). | La presunción de donación opera **recién tras la certificación de muerte**. |
| **Art. 39** | Todo médico que **certifique la muerte** debe iniciar el proceso de donación según normas del INCUCAI. | El deber explícito de iniciar el proceso es **post-mortem**; la detección PSG<7 es **pre-mortem** y se ampara en el Art. 14 + el subprograma de calidad. |
| Funciones INCUCAI | Dicta normas técnicas de **detección, selección y mantenimiento** de potenciales donantes. | El criterio PSG<7 ([19](19_incucai_deteccion_psg7_garantia_calidad.md)) es ejercicio de esta facultad. |

### Ley 26.529 (Derechos del Paciente) — el paciente vivo está protegido
- El paciente es **titular de su historia clínica**; tiene derecho a confidencialidad de sus **datos sensibles**.
- El manejo de información clínica debe observar la Ley **25.326**.

### Ley 25.326 (Datos Personales) — datos de salud = sensibles
- Los **datos de salud son "datos sensibles"** con protección reforzada.
- Tratamiento lícito requiere base legal; el sector salud tiene excepciones para **fines de prestación sanitaria** por profesionales sujetos a secreto.

## Relevancia para el sistema de trasplantes

Da el "permiso para construir" y a la vez fija los límites. **La detección pre-mortem es defendible** porque (a) el Art. 14 la exige al establecimiento, (b) el INCUCAI ya la norma vía PSG<7, y (c) el destinatario natural —el coordinador hospitalario— es **personal del propio establecimiento**, dentro de la relación asistencial. El punto sensible es **notificar a un tercero externo (CUCAI/INCUCAI) con datos identificables antes de la muerte**: ahí conviene minimizar.

## Implicancias tecnológicas / de diseño

- **Mantener la alerta pre-mortem dentro del establecimiento:** avisar al **coordinador hospitalario** (interno), no volcar datos identificables al CUCAI externo hasta que haya base legal más fuerte (p. ej. evolución a muerte encefálica). Alinea con la idea del equipo y con [25_alert_fatigue_cds_uti.md](25_alert_fatigue_cds_uti.md).
- **Minimización de datos:** la alerta inicial puede ser **seudonimizada** (ID de cama/episodio, no nombre) — el patrón FHIR `id-only` lo facilita ([22](22_fhir_subscription_codificacion_glasgow.md)).
- **Trazabilidad y secreto profesional:** todo acceso del coordinador debe quedar auditado; los operadores del adaptador deben estar bajo secreto.
- **DPIA recomendada:** evaluación de impacto en protección de datos antes del piloto, y dictamen de la **AAIP** (autoridad de aplicación de 25.326).

## Citas relevantes

> "Los establecimientos asistenciales que reúnan las características que determine la reglamentación deberán contar con servicios destinados a la donación de órganos y tejidos que permitan la correcta detección, evaluación y tratamiento del donante." (Ley 27.447, Art. 14 — paráfrasis ajustada)

> "Todo médico que mediante comprobación certifique el fallecimiento de una persona, debe dar aviso e iniciar el proceso de donación..." (Ley 27.447, Art. 39 — sentido)

> "...adecuado resguardo de la intimidad del paciente y la confidencialidad de sus datos sensibles, sin perjuicio de lo dispuesto en la Ley N° 25.326." (Ley 26.529)

## Observaciones

- **A verificar (con abogado/AAIP):** el texto literal exacto del Art. 14 y su reglamentación (qué establecimientos están obligados a tener servicio de procuración) — citado aquí en sentido, conviene confirmar el articulado.
- **A verificar:** si el coordinador hospitalario, por su rol legal (Res. 1642/22), tiene acceso habilitado a la HC de pacientes que no atiende directamente — probable que sí dentro de su función, pero conviene dictamen.
- Extiende lo ya documentado de la Ley 27.447: [04_ley_27447_justina.md](04_ley_27447_justina.md).
- El marco habilitante general (la norma NO prohíbe APIs/FHIR/IoT) ya está en research.md §4.
