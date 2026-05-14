# Comparación internacional: UNOS, Eurotransplant, NHS BT, INCUCAI

- **URLs:**
  - https://unos.org/technology/unet-apis/
  - https://unos.org/technology/unet/
  - https://www.eurotransplant.org/manual/chapter-4-kidney-etkas-and-esp/
  - https://www.gov.uk/algorithmic-transparency-records/nhs-blood-and-transplant-organ-offering-scheme-algorithms
  - https://www.odt.nhs.uk/
- **Fecha de consulta:** 2026-05-14

## Resumen comparativo

| Aspecto | INCUCAI (AR) | UNOS (USA) | Eurotransplant (NL/BE/AT/DE/HU/HR/SI/LU) | NHS BT (UK) |
|---|---|---|---|---|
| Sistema central | **SINTRA** (J2EE/Oracle, 2003) | **UNet** (incluye DonorNet, Waitlist, TIEDI) | Base centralizada en Leiden | **UK Transplant Registry** (Oracle + IBM ODM + BAW) |
| APIs públicas | **No documentadas** | **238 APIs, 300+ implementaciones** | Limitadas | Integraciones internas |
| Integración EHR | **No documentada** | **Epic / Cerner via APIs (Phoenix Transplants module)** | Limitada por país | Parcial |
| Algoritmo transparente | **Parcial** (resoluciones publicadas) | **Política OPTN pública + public comment** | Publicado vía PubMed | **Public ATRS record + algorithmic transparency** |
| Cloud | On-premise | **Google Cloud** | On-premise | Sin información pública |
| FHIR | No documentado | En evaluación | No documentado | No documentado |
| Match engine | Score nativo SINTRA | UNet match run | Complex algorithm | **IBM ODM rules engine** |
| Tamaño anual | ~2.000 donantes / ~4.200 trasplantes | ~14.000 donantes / ~46.000 trasplantes | ~7.000 donantes / ~7.500 trasplantes | ~2.000 donantes / ~4.700 trasplantes |
| Cobertura | Nacional federal | 58 OPOs | 8 países | 1 país, 4 reinos |

## Detalles técnicos clave

### UNOS — el más maduro tecnológicamente
- **UNet** es la plataforma central; **DonorNet** maneja órganos, **Waitlist** maneja receptores, **TIEDI** maneja outcomes.
- **Match Run**: algoritmo órgano-específico genera ranking.
- **APIs**: 238 endpoints. Cubren:
  - Submission/retrieval de Waitlist (registro de candidatos).
  - Organ acceptance data por candidato (donor ID, organ type, laterality, match data).
  - Lab data (hígado, pulmón).
  - TIEDI (outcomes post-trasplante).
- **Cloud**: migrado a Google Cloud (case study público).
- **Adopción EHR**: Epic Phoenix Transplants integra directamente con TIEDI.

### Eurotransplant — ETKAS
- Algoritmo único para 8 países (Austria, Bélgica, Croacia, Alemania, Hungría, Luxemburgo, Holanda, Eslovenia).
- Variables incluidas: HLA, ABO, edad, tiempo en espera, balance de intercambio entre países (importante para evitar que un país "exporte" más de lo que "importa").
- Algoritmo bien documentado en literatura científica (PubMed).
- Simulaciones publicadas en protocols.io para reproducibilidad.

### NHS BT — el más transparente
- **Arquitectura**: Oracle + IBM ODM (Decision Manager) + IBM BAW (Business Automation Workflow).
- **Reglas en motor de reglas** → permite auditoría, versionado, simulación.
- **Algorithmic Transparency Standard**: NHS BT publicó su algoritmo bajo el Algorithmic Transparency Recording Standard del Reino Unido.
- **TransplantPath** (2024): read-only system con files/fotos/videos de caracterización del órgano (reemplazó EOS legacy).
- **Auditoría**: revisión trimestral año 1, anual luego, full review cada 5 años.
- **Apelación**: pacientes pueden apelar el resultado del algoritmo.
- **Seguridad**: 2FA, firewalls EAL4, role-based access.

## Lecciones para Argentina

### Lo que se puede adoptar
1. **API pública de SINTRA** estilo UNet (238 APIs es un horizonte).
2. **Motor de reglas** como IBM ODM o Drools — separa lógica de allocation del resto.
3. **Algorithmic Transparency** estilo NHS — publicar algoritmo + simulaciones.
4. **EHR integration** estilo Epic/Cerner — hoy los hospitales argentinos usan distintos HIS, pero sigue siendo viable.
5. **Public comment** en cambios de política (OPTN model).

### Lo que NO copiar
- Fragmentación de OPOs (USA tiene 58 organizaciones independientes — problema documentado).
- Complejidad burocrática del modelo UK Transplant Registry.

## Implicancias tecnológicas

- **Argentina es mediana**: tamaño operativo similar a NHS UK (~2.000 donantes). Permite copiar parcialmente arquitectura UK.
- **La distancia geográfica** argentina (4.000 km) es mayor que cualquier país europeo individual — implica más énfasis en logística aérea que cualquier sistema europeo.
- **Federalismo** argentino se parece más a USA (estados/OPOs) que a sistemas centralizados europeos.

## Citas relevantes

> "There are 238 APIs and 15 applications built by partner organizations totaling over 300 implementations across all the hospitals in the UNOS network." — UNOS

> "TransplantPath, a read-only system launched by NHSBT in March 2024 to replace the legacy Electronic Offering System (EOS)" — NHS BT

> "All transplantation centers within the member states of Eurotransplant have access to the central computer database" — Eurotransplant

## Observaciones

- UNOS, Eurotransplant y NHS BT publican mucho más sobre arquitectura técnica que INCUCAI.
- La falta de información pública sobre arquitectura SINTRA puede ser un problema de transparencia o de simple ausencia de documentación.
- La adopción de FHIR a nivel global en trasplantes está en etapa temprana — Argentina podría ser pionera regional.
