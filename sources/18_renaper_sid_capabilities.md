# RENAPER — SID (Sistema de Identidad Digital) y Mi Argentina

- **URLs:**
  - https://www.argentina.gob.ar/interior/renaper/sid-sistema-de-identidad-digital
  - https://www.argentina.gob.ar/sid/proceso-de-verificacion
  - https://www.argentina.gob.ar/sid/seguridad
  - https://www.argentina.gob.ar/sid/modalidades-y-productos
  - https://www.argentina.gob.ar/miargentina/validar-identidad
- **Fecha de consulta:** 2026-05-14

## Resumen

El **Sistema de Identidad Digital (SID)** del RENAPER permite a organismos públicos y privados validar la identidad de una persona contra la base RENAPER en **menos de 7 segundos**. Soporta biometría facial con liveness, validación de huella, y consulta de DNI. **Mi Argentina** consume el mismo backend SID para validar identidad ciudadana.

## Capacidades técnicas

### Tres servicios SID

| Servicio | Integración | Liveness | Uso típico |
|---|---|---|---|
| **DNI Data Validation** | API REST | N/A | Verifica datos del DNI físico |
| **Facial Photo Validation** | API REST | **No incluido** | Compara foto contra base RENAPER |
| **Fingerprint Validation** | SOAP | No incluido | Compara huella digital |

> ⚠️ **Hallazgo crítico**: el servicio facial via API REST **NO incluye liveness por defecto**. Liveness solo se ejecuta en flujo completo SID (selfie con gestos aleatorios), tipicamente en SDK móvil. Esto tiene **implicancias enormes** para el caso de uso hospitalario (paciente inconsciente no puede hacer gestos).

### Métodos de verificación (cuando hay sujeto consciente)

1. **DNI + Facial con liveness** — Foto frente + dorso DNI + selfie con 2 gestos aleatorios.
2. **Solo facial con liveness** — Número de DNI + género + selfie con 2 gestos.
3. **Validez de documento** — DNI + género + número de trámite.

### Especificaciones de seguridad
- **Cifrado en BD**: AES256.
- **Transporte**: HTTPS TLS 1.2 obligatorio.
- **VPN**: IPsec entre Azure y RENAPER.
- **Firewall**: Aplicación capa 7.
- **Templates biométricos**: NUNCA salen del entorno RENAPER. NUNCA almacenados en dispositivo.
- **API Keys**: autenticación de consumidor + portal self-service.
- **Timestamping**: validación de etapas de transacción (audit trail).
- **Certificaciones ISO**: no documentadas públicamente.
- **Falsos positivos/negativos**: tasas **no publicadas**.

### Datos retornados
- Score / porcentaje de coincidencia.
- Datos públicos de la persona en cuestión (nombre, apellido, DNI, fecha nacimiento, género, domicilio según RENAPER, etc.).
- Validez del documento.

## Marco normativo de uso

- **Ley 25.326** — Protección de Datos Personales.
- **Artículo 11 Ley 25.326** — Cesión de datos solo para fines directamente relacionados con interés legítimo de ambas partes, con consentimiento previo del titular del dato (con excepciones, como interés público sanitario debidamente fundado).
- **Convenio Único de Verificación de Datos** — todo consumidor SID debe firmarlo vía TAD (Trámites a Distancia).
- **Contacto convenios**: `conveniosid@renaper.gob.ar`

## Adopción actual

- **BCRA** (Banco Central) — primer organismo en certificar uso.
- **AFIP, ANSES** — uso amplio.
- **OSDE, prepagas, bancos** — uso comercial.
- **No hay caso de uso documentado en INCUCAI ni en sistema de trasplantes**.

## Limitaciones técnicas para uso hospitalario

| Limitación | Impacto en caso de uso "identificación de potencial donante" |
|---|---|
| Liveness requiere gestos | Inutilizable directamente en paciente inconsciente |
| API REST facial sin liveness disponible | Posible pero requiere mitigaciones anti-spoofing alternativas |
| No documentadas tasas FAR/FRR | Riesgo: difícil dimensionar margen de error legal |
| Sin SDK específico para entorno clínico | Hardware (cámara, iluminación) debe estandarizarse |
| Sin certificaciones ISO públicas | Compliance médico requiere validación adicional |
| Convenio vía TAD | Tiempos burocráticos (semanas/meses) |

## Capacidades complementarias relevantes

### Mi Argentina
- App ciudadana con DNI digital + credenciales.
- Validación de identidad SID al onboarding (selfie + gestos + DNI).
- Útil para que **el donante registre su voluntad** y para que **la familia confirme su identidad** durante el operativo.

### Huella digital RENAPER
- Disponible para todos los argentinos.
- API SOAP.
- **No requiere consciencia del sujeto** — viable en paciente inconsciente.
- Sensor capacitivo / óptico estándar de mercado.

## Relevancia para Casa Justina / detección de donantes

### Caso de uso real (acotado pero valioso)
1. **Pacientes NN (no identificados)** — víctimas de trauma, accidentes, sin DNI. Identificarlos rápido habilita:
   - Consulta del registro de manifestación de voluntad (positiva/negativa).
   - Contacto con familia (RENAPER tiene domicilio).
   - Inicio del workflow de procuración sin esperar identificación judicial.
2. **Cross-validación** en pacientes con DNI sospechoso (homonimias, DNI dañados).
3. **Wristband + biometría** durante estancia, para evitar errores de identidad en operativos multi-órgano.

### Caso de uso NO viable
- Scan masivo de pacientes UTI buscando "potenciales donantes" → invasivo, ilegal, redundante (no hace falta facial reconocimiento para saber si un paciente con DNI puede ser donante).

## Citas relevantes

> "Los valores biométricos de los ciudadanos no salen del entorno seguro del RENAPER ni son almacenados en el dispositivo." (Seguridad SID)

> "En menos de 7 segundos podrás continuar con tu trámite online." (SID)

> "Las entidades que deseen acceder al SID deberán cumplir con la Ley N° 25.326 de Protección de Datos Personales y normas complementarias." (Modalidades SID)

## Observaciones

- SID es **maduro tecnológicamente** y cubierto normativamente — el bloqueo NO es técnico-legal, es de **diseño del caso de uso**.
- La huella digital es probablemente **más viable que facial** en entorno hospitalario con paciente inconsciente.
- Combinar facial + huella + DNI (cuando lo haya) reduce falsos positivos a niveles aceptables.
- El convenio con RENAPER es accesible para hospitales públicos. Hospitales privados requieren más trabajo administrativo.
