# Matching, listas de espera y criterios de asignación en Argentina

- **URLs:**
  - https://www.argentina.gob.ar/salud/incucai/pacientes/distribucion-hepatica
  - https://le.incucai.gov.ar/public/Modulo2.do
  - https://www.santafe.gov.ar/index.php/web/content/view/full/142133/(subtema)/141865
- **Fecha de consulta:** 2026-05-14

## Resumen

El matching y asignación de órganos en Argentina se realiza dentro de SINTRA mediante algoritmos órgano-específicos basados en criterios médicos objetivos. Las listas de espera son nacionales pero la distribución es regionalizada.

## Algoritmos por órgano

### Riñón — Score por sumatoria
Variables del score:
1. **Antigüedad en diálisis** (puntaje por años).
2. **Situación inmunológica** (pacientes hiperinmunizados con anticuerpos anti-HLA).
3. **Compatibilidad ABO** (grupo sanguíneo).
4. **Compatibilidad HLA** (antígenos compartidos donante/receptor).
5. **Regionalidad** (prioridad zonal).

Cada variable suma puntos → score total → orden de asignación.

### Hígado — MELD-Na / PELD
- **MELD-Sodium** para pacientes ≥12 años.
- **PELD** (Pediatric End-Stage Liver Disease) para <12 años.
- Marco regulatorio: **Resolución INCUCAI 82/22**; Anexo IV reemplazado por **Resolución 128/25**.
- Categorías de prioridad:
  1. **Emergencia** (falla hepática fulminante).
  2. **Electivos** ordenados por MELD/PELD descendente.
- Puntos extra por:
  - Pediátrico (<18 años).
  - Lista hepatointestinal.
  - Situaciones especiales aprobadas por equipos.
- Desempate: tipo O, antigüedad en lista.

### Corazón / Pulmones
Criterios: urgencia clínica, compatibilidad, tiempo en lista. Manejados por subcomisiones de INCUCAI.

### Córneas
Lista regional por antigüedad. Sin urgencia hemodinámica.

## Proceso de asignación

1. Operativo abierto → datos del donante cargados a SINTRA.
2. SINTRA cruza con lista nacional de receptores activos.
3. SINTRA emite **lista ordenada** por órgano.
4. Oferta secuencial: contacto al equipo de trasplante del receptor #1.
5. Equipo evalúa → acepta/rechaza en plazo limitado.
6. Si rechaza → pasa al #2, y así sucesivamente.

## Relevancia para el sistema de trasplantes

El motor de matching de SINTRA es **funcionalmente comparable** a:
- ETKAS (Eurotransplant)
- UNet (UNOS — DonorNet + Waitlist)
- IBM ODM rules engine (NHS BT)

Sin embargo, Argentina:
- No publica el código del algoritmo abiertamente (a diferencia de NHS, que lo hizo público vía algorithmic transparency).
- No ofrece API para integrar resultado del match con sistemas hospitalarios.
- La oferta secuencial es **manual** (llamada telefónica/email).

## Implicancias tecnológicas

### Limitaciones actuales
- **Latencia humana en cada rechazo**: cada oferta rechazada agrega minutos al CIT.
- **Falta de aceptación parallel**: en algunos sistemas modernos (UNOS) se permiten ofertas múltiples paralelas en categorías especiales.
- **Datos clínicos del receptor desactualizados**: si MELD/peso/datos no se actualizan en SINTRA, asignación es subóptima.

### Oportunidades
- **API para EHR**: el equipo de trasplante podría aceptar/rechazar desde su sistema (no entrar a SINTRA).
- **Tablero de oferta en tiempo real** con countdown.
- **Pre-screening automatizado**: si el equipo tiene reglas de exclusión, declinar automáticamente sin esperar humano.
- **Predicción de aceptación**: ML para priorizar candidatos con alta probabilidad de aceptación → reducir cadena de rechazos.
- **Auditoría algoritmica transparente** (como NHS BT).

## Citas relevantes

> "La asignación de órganos y tejidos se hace exclusivamente en base a criterios médicos objetivos: la urgencia según la gravedad del paciente, la compatibilidad entre donante y receptor, la oportunidad del trasplante, el tiempo en lista de espera y demás criterios médicos aceptados."

## Observaciones

- Argentina carece de un sistema análogo al **OPTN policy public comment** de EE.UU., donde cualquier cambio de algoritmo se publica para consulta pública.
- La regionalidad como variable de score es razonable por logística pero puede generar inequidad entre provincias.
- El score renal es estable conceptualmente desde hace años; podría enriquecerse con KDPI (Kidney Donor Profile Index) y EPTS (Estimated Post-Transplant Survival) como ya usan UNOS.
