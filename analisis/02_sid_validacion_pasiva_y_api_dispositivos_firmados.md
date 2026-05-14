# Análisis complementario — Liveness pasivo "estilo MercadoLibre" y API especial para dispositivos firmados

> Complemento a [01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md)
>
> Pregunta a resolver:
> 1. Si el SID del RENAPER acepta validación pasiva (solo acercar la cámara, sin gestos) — ¿se puede usar con paciente inconsciente?
> 2. ¿Es viable negociar con RENAPER una API especial para **dispositivos firmados** (device-attested) que no requiera liveness?
>
> **Fecha:** 2026-05-14

---

## 0. TL;DR

| Pregunta | Respuesta breve |
|---|---|
| ¿Liveness pasivo del estilo MercadoLibre funciona con paciente inconsciente? | **Parcialmente.** El liveness pasivo analiza textura/depth/microflujo. Un paciente vivo, aunque inconsciente, **mantiene piel viva → puede pasar la prueba**. **Pero RENAPER no ofrece liveness pasivo certificado en su API estándar pública**. Se podría implementar liveness pasivo del lado del cliente con vendor externo (Veridas, Innovatrics, FacePhi) y enviar a SID el match facial puro. |
| ¿Se puede negociar una API especial sin liveness para dispositivos firmados? | **Sí, técnicamente y políticamente factible**, pero requiere convenio especial RENAPER + Ministerio del Interior + INCUCAI con figura legal de "uso de excepción para emergencia sanitaria". Hay precedente: el BCRA negoció su modalidad antes que nadie. Tiempo estimado: **6-12 meses de gestión**. |

---

## 1. Cómo funciona el liveness pasivo "estilo MercadoLibre"

### Diferencia clave: activo vs pasivo

| Tipo | Lo que pide al usuario | Tecnología | Funciona con inconsciente? |
|---|---|---|---|
| **Activo (legacy SID Mi Argentina)** | Gestos aleatorios (mover cabeza, parpadear, sonreír) | Detecta movimiento causado por instrucción | **No** (paciente no obedece) |
| **Pasivo (MercadoLibre, fintechs modernas)** | Solo poner la cara frente a la cámara | Analiza textura de piel, micro-flujo sanguíneo subcutáneo, paralaje, frecuencias de Moiré, profundidad estéreo | **Sí** (la piel viva sigue mostrando signos biológicos) |
| **Híbrido** | Cara frente + a veces un gesto único | Combinación | Parcialmente |

### Por qué pasivo SÍ debería funcionar con paciente inconsciente

El liveness pasivo certificado ISO/IEC 30107-3 detecta **vida biológica**, no **cooperación cognitiva**. Las señales que analiza son:
- **Textura de piel viva** (poros, vellosidad, irregularidades): un paciente comatoso las tiene.
- **Pulso subcutáneo** (rPPG — micro-color shifts de la sangre bajo la piel): **paciente con presión arterial detectado como vivo**.
- **Paralaje en 3D** (la cara tiene profundidad real, no es una foto 2D): irrelevante a la consciencia.
- **Reflejos especulares** de córnea/piel: presentes aunque los ojos estén cerrados.
- **Termografía** (en sensores con IR): paciente caliente = vivo.

⚠️ **Excepciones donde puede fallar**:
- Paciente en muerte encefálica con perfusión mantenida: pulso presente → **pasaría como vivo, correcto para identificación, pero ético/legalmente sensible**.
- Paciente fallecido sin perfusión: pulso ausente → falla pasivo → **necesita override autorizado**.

### Lo que **NO** ofrece RENAPER hoy

| Producto SID actual | Liveness incluido | Disponible para uso hospitalario? |
|---|---|---|
| SDK Mi Argentina (selfie + gestos) | **Activo, obligatorio** | No (paciente no coopera) |
| Servicio "Validación Facial" REST | **No incluido** — match contra base, pero sin PAD | **Sí técnicamente, pero el contratante asume el riesgo del anti-spoofing** |
| Servicio "Validación Huella" SOAP | N/A | Sí |

→ La **API REST de "Validación Facial" del SID acepta una foto y devuelve un match**, pero **la responsabilidad de demostrar que la foto es de una persona real, viva, presente** queda en el consumidor. Esto deja la puerta abierta a:

1. **Implementar liveness pasivo del lado cliente** (vendor externo certificado iBeta ISO 30107-3 Nivel 2).
2. **Confirmar liveness por otro factor** (presencia física + signos vitales del monitor médico + huella).
3. **Override autorizado por médico** con justificación clínica grabada en el audit log.

### Vendors de liveness pasivo certificados

| Vendor | Certificación | Modelo de deployment | Notas |
|---|---|---|---|
| **Veridas** (España) | iBeta L1/L2 + ISO 30107-3 | Cloud + on-prem | Presencia regional Latam |
| **Innovatrics** (Eslovaquia) | NIST/NVLAP L1/L2 + iBeta L1/L2 | SDK + Cloud | Usado por bancos LatAm |
| **FacePhi** (España) | iBeta L1/L2 | SDK | Fuerte en banca |
| **ID R&D** (USA) | ISO 30107-3 Nivel 2 | SDK | Pasivo puro |
| **IDmission** | iBeta L1/L2 | Cloud | Pasivo |
| **Paravision** | NIST | SDK | Más enterprise |

**Costo orientativo:** USD 0,10 – 0,50 por verificación, según volumen. Para 500 operativos/año = USD 50–250/año. **Negligible.**

### Recomendación arquitectónica revisada

```
Paciente NN (inconsciente)
        │
        ▼
[Cámara hospitalaria]
        │
        ▼
┌───────────────────────────────────────────────┐
│ Cliente DonorID — captura cara + huella       │
│ ┌─────────────────────────────────────────┐   │
│ │ 1) Veridas/Innovatrics SDK              │   │
│ │    → verifica liveness pasivo           │   │
│ │    → CONFIRMA cara viva real            │   │
│ └─────────────────────────────────────────┘   │
└──────────────┬────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────┐
│ Gateway DonorID — backend                    │
│  - Recibe imagen + token liveness            │
│  - Envía a SID "Validación Facial" REST      │
│  - Recibe match + datos persona              │
│  - Envía huella a SID "Validación Huella"    │
│  - Recibe match huella                       │
│  - Cruza con SINTRA → voluntad + contacto    │
└──────────────────────────────────────────────┘
```

**Beneficio:** RENAPER recibe una foto cuya "vivacidad" fue certificada por vendor externo iBeta. RENAPER hace el matching que es su core. Cada parte hace lo que mejor sabe.

---

## 2. ¿Se puede negociar una API especial sin liveness para dispositivos firmados?

### Concepto: "device attestation" o "atestación de dispositivo"

El RENAPER podría definir una nueva **modalidad de acceso al SID** donde:
- El consumidor opera **hardware certificado** (dispositivo con TPM / Secure Enclave / TEE).
- Cada captura va **firmada criptográficamente** por el dispositivo (`signed_attestation`).
- El dispositivo **garantiza** físicamente que la captura es real (cámara/sensor empotrado, no foto desde el rolo).
- El acceso queda restringido a **organismos sanitarios habilitados** + **uso en emergencia médica documentada**.

Esta arquitectura es estándar en otros dominios:
- **Apple App Attest / DeviceCheck** — Apple certifica que la app corre en hardware genuino.
- **Android Play Integrity / Hardware Attestation** — análogo Android.
- **WebAuthn / FIDO2** — autenticación basada en device attestation.
- **PIV smart cards** (gobierno USA) — el chip atesta la operación.

### Por qué es factible para RENAPER

**Argumentos a favor:**
1. **Precedente BCRA**: el SID inicialmente solo era para gobierno. El BCRA negoció su tier para fintechs. Ahora hay múltiples modalidades. **Precedente probado de modalidades especiales.**
2. **Marco normativo:** Ley 25.326 Art. 5 inc. 2 — habilita uso sin consentimiento individual cuando hay **interés vital sanitario**. Donación de órganos califica.
3. **Antecedente operativo:** la consulta al registro de voluntad por DNI ya se hace cuando hay donante presunto sin consentimiento explícito previo. Es una operación de **cesión de datos por interés vital**.
4. **Volumen acotado:** 200-500 operativos/año → **no es masivo** → riesgo de mal uso controlable.
5. **Trazabilidad:** Art. 38 Ley 27.447 obliga a registro trazable → mecanismo de auditoría coincide con lo que pediría RENAPER.

**Argumentos en contra:**
1. **Inercia institucional** — RENAPER puede no priorizar el caso por volumen bajo.
2. **Riesgo reputacional** — si un dispositivo "firmado" se hackea, escándalo grande.
3. **Costo de desarrollo** del nuevo tier API — no es trivial; podría tomar 12+ meses.

### Estructura propuesta del convenio "SID Médico de Emergencia"

```
┌──────────────────────────────────────────────────────────────┐
│ Convenio Tripartito: RENAPER + INCUCAI + Hospital            │
│                                                              │
│ Artículo 1 — Naturaleza                                      │
│   Modalidad especial SID para identificación de pacientes    │
│   en emergencia médica con potencial donación de órganos.    │
│                                                              │
│ Artículo 2 — Dispositivos habilitados                        │
│   Solo dispositivos certificados (chip TPM/SE) + firmware    │
│   firmado + clave única por unidad + registro en RENAPER.    │
│                                                              │
│ Artículo 3 — Operadores habilitados                          │
│   Personal médico identificado con MFA + matrícula           │
│   profesional + capacitación previa.                         │
│                                                              │
│ Artículo 4 — Casos de uso                                    │
│   - Paciente NN inconsciente / fallecido                     │
│   - Cross-validación de identidad en duda                    │
│   - Confirmación de identidad para certificación de muerte   │
│   PROHIBIDO: uso masivo, screening, vigilancia.              │
│                                                              │
│ Artículo 5 — Liveness                                        │
│   Exento por incapacidad del sujeto de cooperar.             │
│   Reemplazado por:                                           │
│   - Atestación del dispositivo firmado                       │
│   - Confirmación humana del operador (firma digital)         │
│   - Segundo factor obligatorio (huella RENAPER)              │
│                                                              │
│ Artículo 6 — Auditoría                                       │
│   Cada query firmada por operador + dispositivo + timestamp  │
│   RENAPER. Logs replicados en AAIP cada 24h. Acceso a        │
│   auditor externo anual.                                     │
│                                                              │
│ Artículo 7 — Datos retornados                                │
│   Mínimos suficientes: nombre, DNI, género, fecha nac.,      │
│   domicilio para contacto familiar. NO se devuelven datos    │
│   no necesarios.                                             │
│                                                              │
│ Artículo 8 — Sanciones                                       │
│   Uso indebido = revocación de credenciales + denuncia       │
│   penal Art. 157bis CP.                                      │
└──────────────────────────────────────────────────────────────┘
```

### Tiempo realista de negociación

| Etapa | Tiempo estimado | Actor crítico |
|---|---|---|
| Documento técnico propuesta inicial | 2 semanas | Equipo proyecto |
| Reuniones con RENAPER (Convenios SID) | 1-2 meses | Casa Justina + AFIDEER |
| Dictamen jurídico interno RENAPER | 1-2 meses | RENAPER legal |
| Dictamen AAIP | 2-3 meses | AAIP |
| Convenio tripartito firmado | 1-2 meses | RENAPER + INCUCAI + Hospital |
| Desarrollo de la modalidad API (lado RENAPER) | 3-6 meses | RENAPER técnico |
| **Total optimista** | **8-12 meses** | |
| **Total realista** | **12-18 meses** | |

→ **No es viable como entregable del Desafío 2026 (deadline noviembre).** Sí es viable como **roadmap post-piloto**.

### Plan dual para el desafío

| Track | Plazo | Qué incluye |
|---|---|---|
| **Track A — MVP demostrable (Desafío AFIDEER nov 2026)** | 3-6 meses | Liveness pasivo Veridas/Innovatrics + SID estándar + mock SINTRA |
| **Track B — Producción con dispositivos firmados** | 18-24 meses | Convenio especial RENAPER + hardware certificado + integración SINTRA real |

Presentar Track A como entregable y Track B como visión de escala → demuestra **profundidad estratégica**, no solo el demo.

---

## 3. Riesgo específico: paciente fallecido con perfusión cesada

### Escenario crítico no resuelto por liveness pasivo

Paciente con paro cardíaco prolongado → no hay pulso subcutáneo → liveness pasivo **falla**.

¿Cómo identificamos a un paciente fallecido si el liveness exige vida biológica?

**Opciones:**

1. **Override autorizado** (recomendado) — operador médico con matrícula declara que el sujeto está fallecido y solicita identificación bajo Art. 38 Ley 27.447. Cada override queda en audit log. El médico asume responsabilidad legal.

2. **Bypass por modalidad especial** — la modalidad "SID Médico de Emergencia" (Track B) **no requiere liveness** porque el contexto (paciente en UTI o morgue, hospital, operador certificado) ya asegura presencia física.

3. **Huella como factor único** — si no hay liveness facial confiable, la huella RENAPER es válida sola para identificación (los muertos tienen huellas idénticas a las vivas).

→ **La huella es el factor más robusto en este caso de uso.** Facial es secundario.

---

## 4. Recomendación final para el desafío

### Arquitectura técnica recomendada para el MVP

```
┌──────────────────────────────────────────────────────────────┐
│ Captura en hospital                                          │
│  • Cámara IP fija o tablet con cámara                        │
│  • Sensor de huella USB (ver análisis BOM)                   │
└──────────────────────────┬───────────────────────────────────┘
                           │
              ┌────────────▼────────────┐
              │ Cliente DonorID         │
              │ ┌─────────────────────┐ │
              │ │ Vendor liveness     │ │ ← Veridas / Innovatrics SDK
              │ │ pasivo (cliente)    │ │   (iBeta L2 cert)
              │ └─────────────────────┘ │
              │                         │
              │ ┌─────────────────────┐ │
              │ │ Captura huella      │ │
              │ └─────────────────────┘ │
              │                         │
              │ Si falla liveness:      │
              │   override autorizado   │ ← Firmado por médico
              │   (audit log)           │   con matrícula
              └────────────┬────────────┘
                           │ HTTPS + atestación dispositivo
              ┌────────────▼────────────┐
              │ Gateway DonorID          │
              │  - Llama SID Facial      │ ← MATCH facial (sin liveness, ya certificada)
              │  - Llama SID Huella      │ ← MATCH huella
              │  - Combina scores         │
              │  - Decide identidad       │
              │  - Consulta SINTRA        │
              │  - Audit log inmutable    │
              └──────────────────────────┘
```

### Mensajes clave para presentar a Casa Justina / AFIDEER

1. **Sí se puede prescindir del liveness con gestos** — usando liveness pasivo del lado cliente + huella + atestación.
2. **El liveness pasivo certificado iBeta L2 es producto de mercado** — no hay que inventar nada, solo integrar.
3. **El convenio especial "SID Médico de Emergencia" es la jugada de largo plazo** — viable, con precedente (BCRA), pero tarda 12-18 meses.
4. **La huella es el factor más confiable** en este caso de uso — facial es complemento, no protagonista.
5. **El override médico** cubre el caso "paciente fallecido sin perfusión" — legalmente sólido bajo Art. 38 Ley 27.447 + matrícula del operador.

### Pitch para el desafío

> No proponemos saltarnos el liveness por conveniencia. Proponemos **reemplazar el liveness por gestos** (imposible con paciente inconsciente) **por una combinación más robusta**: liveness pasivo certificado iBeta, huella RENAPER, override médico documentado y atestación de dispositivo. El resultado es **más seguro** que el SID actual contra fraude, **funcional** con pacientes inconscientes/fallecidos, y **legalmente trazable** según Ley 27.447.

---

## 5. Costos del componente de liveness

| Componente | Modelo | Costo USD |
|---|---|---|
| Veridas SDK (cloud) | Pay-per-verify | ~0,10 – 0,30 |
| Innovatrics OnPrem | Licencia anual ilimitada | ~5.000 – 20.000/año |
| ID R&D SDK | Licencia perpetua | ~10.000 – 30.000 one-time |
| Open source (BioID, propio) | DIY | gratis pero sin certificación → no recomendado |

Para 500 verificaciones/año con vendor cloud: **USD 50-150 anuales**. Despreciable.

---

## 6. Ficheros relacionados

- [01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md) — análisis maestro
- [03_sensor_huella_costo_bom.md](03_sensor_huella_costo_bom.md) — BOM hardware sensor huella
- [../sources/18_renaper_sid_capabilities.md](../sources/18_renaper_sid_capabilities.md) — capacidades RENAPER documentadas
