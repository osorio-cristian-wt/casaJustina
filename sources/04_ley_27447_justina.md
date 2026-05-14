# Ley 27.447 — "Ley Justina"

- **URL:** https://servicios.infoleg.gob.ar/infolegInternet/verNorma.do?id=312715
- **URL secundaria:** https://www.argentina.gob.ar/sites/default/files/ley-27447.pdf
- **Sancionada:** 4 de julio de 2018; vigente desde 4 de agosto de 2018.
- **Decreto reglamentario:** 16/2019 (BO 07/01/2019).

## Resumen del contenido

Ley nacional que regula la obtención y utilización de órganos, tejidos y células de origen humano. Introduce el principio de **donante presunto** (toda persona mayor de 18 años es donante salvo manifestación expresa en contrario) y simplifica trámites.

## Información clave

### Cambios estructurales

- **Donante presunto** (Art. 33) — opción negativa explícita.
- **INCUCAI** designado como **autoridad de aplicación**.
- Crea la **Comisión Federal de Trasplantes (COFETRA)** (Art. 50–53).
- Habilita **trazabilidad obligatoria** desde la procuración hasta el implante (Art. 38).
- Define **SINTRA** como el sistema de información obligatorio (Art. 50).
- Plazo máximo de 6 horas desde declaración de muerte hasta entrega del cuerpo.
- Cobertura **100% por obras sociales y prepagas** (Art. 64).
- Inscripción en lista de espera unificada (Art. 39).

### Confidencialidad y datos

- Datos del donante y receptor confidenciales (Art. 38, 47).
- INCUCAI debe garantizar trazabilidad bidireccional.

### Coordinación federal

- Cada jurisdicción debe contar con organismo provincial de procuración.
- INCUCAI coordina con CUCAI provinciales vía COFETRA.

## Relevancia para el sistema de trasplantes

Marco normativo **base** para cualquier sistema tecnológico que se construya sobre el ecosistema argentino:

- Cualquier solución debe estar **integrada con SINTRA** (no reemplazarlo) — Art. 50 lo hace obligatorio.
- Trazabilidad bidireccional (Art. 38) es un requerimiento legal — útil para arquitecturas event-sourcing.
- Confidencialidad de datos exige cifrado, anonimización, control de acceso por rol.

## Implicancias tecnológicas

- La ley **no prohíbe** la integración de SINTRA con otros sistemas vía APIs.
- La ley **no especifica** estándares técnicos (HL7/FHIR no son mencionados).
- La obligatoriedad de SINTRA crea un **punto único de verdad** legal — facilita arquitecturas integradoras.
- Donante presunto reduce trabajo de gestión de consentimiento individual, pero exige consulta rápida al registro de oposición.

## Citas relevantes

Art. 50: "El INCUCAI tendrá a su cargo el desarrollo, administración y fiscalización del Sistema Nacional de Información de Procuración y Trasplante (SINTRA), de uso obligatorio."

Art. 38: "Debe garantizarse en todo momento la trazabilidad de órganos, tejidos y células."

## Observaciones

- La ley fija el "QUÉ" pero deja amplio margen al INCUCAI para definir el "CÓMO" tecnológico.
- No menciona interoperabilidad con sistemas hospitalarios (HIS/EHR).
- Es compatible con la incorporación posterior de FHIR/HL7 vía resolución del INCUCAI.
- La **Ley de Datos Personales 25.326** y la futura LGDP impactan el manejo de datos en cualquier sistema integrador.
