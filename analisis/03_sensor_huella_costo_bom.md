# Análisis de costos — Sensor de huella portátil para hospital (BOM en USD)

> Complemento a [01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md) y [02_sid_validacion_pasiva_y_api_dispositivos_firmados.md](02_sid_validacion_pasiva_y_api_dispositivos_firmados.md)
>
> Objetivo: estimar el costo en **USD por unidad** de un dispositivo portátil de captura de huella digital diseñado para entornos clínicos: fácil de limpiar (alcohol-resistant), portátil, con conectividad celular opcional, listo para integrar con el SID del RENAPER.
>
> **Fecha:** 2026-05-14

---

## 0. TL;DR — tres tiers de costo

| Tier | Caso de uso | BOM USD | CIF Argentina (50-70% extra) | Precio sugerido por unidad |
|---|---|---|---|---|
| **A — Prototipo / Demo AFIDEER** | Desafío + pruebas internas | **~$45** | ~$75 | No comercializable |
| **B — Piloto certificado** | Piloto hospital real | **~$300** | ~$500 | $700 |
| **C — Producción serializable** | Despliegue 50+ unidades | **~$220** (con descuento volumen) | ~$370 | $550 |

**Conclusión rápida:** un dispositivo portátil clínico de **calidad piloto** (Tier B) cuesta alrededor de **USD 300 en componentes**, equivalente a ~USD 500 instalado en Argentina considerando impuestos. Muy por debajo del costo de un solo operativo fallido por demora en identificación.

---

## 1. Requerimientos del dispositivo

Antes de la BOM, definamos qué tiene que cumplir:

| Requerimiento | Criterio | Implicancia |
|---|---|---|
| **Captura de huella** | Calidad mínima FAP20 (FBI-PIV preferido) | Sensor capacitivo o óptico certificado |
| **Conectividad** | WiFi hospitalario + LTE como backup | Módulo dual; LTE para hospitales con WiFi inestable |
| **Autonomía** | 8 horas mínimo de uso intermitente | Batería LiPo 2000-3000 mAh |
| **Portabilidad** | < 300 g, dimensiones bolsillo | Encapsulado pequeño |
| **Limpieza** | Resistente a alcohol isopropílico 70% y amonio cuaternario | Plástico médico (PC o ABS) + IPX5 mínimo |
| **Sin crevices** | Superficies lisas sin grietas para limpieza | Diseño industrial cuidado, juntas pegadas no atornilladas externas |
| **Identificación visual** | LED RGB + buzzer | Confirmar éxito/fallo de lectura |
| **Seguridad** | Comunicación cifrada + clave única | TPM/SE chip o ESP32-S3 con Secure Boot |
| **Operador** | Botón físico para iniciar lectura | Evitar uso accidental |
| **Audit** | Cada lectura firmada por dispositivo | Atestación de hardware |
| **Carga** | USB-C, idealmente carga inalámbrica | Compatibilidad con cualquier hospital |
| **Cumplimiento** | No es dispositivo médico clase II — accesorio de identificación | Sin ANMAT, pero IEC 60601 deseable |

---

## 2. Tier A — Prototipo / Demo AFIDEER (~$45)

Para demostrar funcionalidad. No certificable. Sirve para presentar en el desafío y desarrollar el software.

| Componente | Modelo | USD c/u | Notas |
|---|---|---|---|
| Sensor de huella capacitivo | **Grow R503** (RGB ring) | $20 | UART + táctil, 64×64 px capacitivo, comunidad ESPHome lo soporta nativamente |
| MCU + WiFi/BT | **ESP32-S3 DevKitC** | $7 | WiFi + BT5 + Secure Boot V2 + USB-OTG |
| Batería LiPo | 3.7V 2000 mAh | $7 | Compatible TP4056 |
| Cargador batería | TP4056 USB-C breakout | $1.50 | Protección sobrecarga |
| Pantalla OLED | 0.96" I²C 128×64 | $3 | Feedback al operador |
| Buzzer activo | 5V | $0.50 | Confirmación auditiva |
| LED RGB WS2812 | 1 unidad | $0.30 | Estado visual |
| Botón táctil | Momentary, encapsulado | $1.50 | Trigger captura |
| Encapsulado | 3D-print PETG food-grade | $5 | Diseño propio, sin certificación |
| Cableado, conectores, pin headers | Surtido | $3 | |
| USB-C breakout | DIY o módulo | $1 | Carga |
| **Total BOM Tier A** | | **~$49** | |
| + Logística + import AR (50-70%) | | | ~$75-85 instalado |

⚠️ El **R503** no es FBI-certified. Su tasa de error es aceptable para POC/demo pero **no para producción clínica**.

### Plataforma de software Tier A
- Firmware Arduino/PlatformIO con la librería [R503-Fingerprint-Sensor-Library](https://github.com/mpagnoulle/R503-Fingerprint-Sensor-Library).
- ESPHome alternativa (más simple para demo).
- Captura → POST HTTPS al gateway DonorID.

---

## 3. Tier B — Piloto certificado en hospital real (~$300)

Para deployment en un hospital piloto. Mínimo certificable. Permite recoger evidencia real.

| Componente | Modelo | USD c/u | Notas |
|---|---|---|---|
| **Sensor de huella FBI-PIV certificado** | **Suprema BioMini Slim 2** (FAP20) | **$180** | USB, ultra-delgado, multi-dynamic range, IP65, formato compacto |
| Plataforma de cómputo | **LilyGo T-SIM7600G-H R2** (ESP32 + 4G LTE + GPS) | **$50** | ESP32-WROVER, 4G LTE, GPS, batería holder 18650 incluido |
| Batería 18650 Li-ion (×2 paralelo) | 3500 mAh c/u | $14 | Reemplazables |
| Antena LTE + GPS | Incluidas o externas | $5 | Cobertura interior hospital |
| Antena WiFi/BT | Cerámica | $2 | |
| OLED I²C 1.3" (más grande, lectura clara) | 128×64 | $5 | |
| Buzzer + LED RGB | | $2 | |
| Lector NFC/RFID PN532 | Opcional, para tag de operador | $5 | Auth por tarjeta del personal |
| Botón físico industrial | IP67, con ring LED | $4 | Trigger limpio |
| Chip Secure Element ATECC608B | Atestación + cifrado | $3 | Clave única dispositivo |
| USB-C (carga + datos) | | $2 | |
| **Encapsulado polycarbonato + TPU** | Diseño industrial, IP65, alcohol-resistant | **$25** | Inyectado o CNC, sin crevices |
| Sello / juntas EPDM | Compatibles con isopropanol 70% | $3 | |
| Pasta térmica + ferrules | | $1 | |
| Tornillería inox + insertos | | $3 | |
| **Cableado interno + flex PCB** | | $5 | |
| Certificado FCC/CE / aceptación nacional | Pro-rata por unidad | $5 | Trámite ENACOM si lleva radio AR |
| **Total BOM Tier B** | | **~$313** | |
| + Logística + impuestos AR | | | ~$520 instalado |

### Por qué Suprema BioMini Slim 2
- **FBI-PIV + Mobile ID FAP20 certificado** → cumple estándar técnico para uso forense/oficial.
- **MDR (Multi-Dynamic Range)** → captura huellas en condiciones difíciles (piel seca, mojada).
- **IP65** → resistente a polvo y agua, viable para entorno clínico.
- **USB** → no requiere driver complejo, multi-OS.
- **Imagen capturada compatible WSQ** → formato estándar que el SID acepta vía SOAP.

### Alternativas Tier B (rango $150-220)
- **DigitalPersona U.are.U 4500** (~$80-180): muy usado en AR (AFIP, bancos). **Pero NO es FAP-cert**, es PIV con limitaciones de captura.
- **Aratek A700 FAP30**: más grande, $150-220.
- **SecuGen Hamster Pro 20**: ~$80-120, FBI cert, no tan compacto.

---

## 4. Tier C — Producción serializable con descuentos de volumen (~$220)

Con compra de 50+ unidades, los componentes bajan ~25-30 %.

| Componente | Costo unitario @50+ unidades USD |
|---|---|
| Sensor (Suprema o equivalente OEM con licenciamiento) | $130 |
| Plataforma ESP32 + LTE (PCB propia, no LilyGo) | $30 |
| Batería 18650 ×2 | $11 |
| Antenas | $4 |
| OLED | $3 |
| LEDs + Buzzer + Botón | $4 |
| Secure Element | $2 |
| USB-C | $1.50 |
| Encapsulado inyectado (molde amortizado) | $15 |
| Juntas / sello | $2 |
| PCB + ensamblaje | $10 |
| Certificación (ENACOM, prorrata) | $3 |
| Calibración + QA por unidad | $5 |
| **Total BOM Tier C @50+** | **~$220** |
| + Logística + impuestos AR | ~$370 instalado |
| **Precio sugerido por unidad (margen 50%)** | **$550** |

### Costo de molde inicial (one-time)
Inyección de plástico medical-grade requiere molde:
- Molde inyección 1+1 cavidades + acabado mate: **$3.000-8.000 one-time**.
- Se amortiza en 50-100 unidades.

→ Para piloto de 5-10 unidades, **mantenerse en Tier B con encapsulado CNC**.

---

## 5. Características clínicas críticas (limpieza y manipulación)

### Resistencia química requerida

El dispositivo debe sobrevivir a **al menos 50 limpiezas/día** con:
- **Alcohol isopropílico 70%** (estándar hospital argentino)
- **Amonio cuaternario** (segunda opción)
- **Hipoclorito 1000 ppm** (ante derrame de fluidos)

Materiales recomendados:
- **Policarbonato (PC)** — resistente a IPA, transparente o opaco.
- **PC/ABS blend** — más barato, similar resistencia.
- **TPU shore 80A** para juntas — flexible y resistente.
- **Silicona médica grado USP VI** para sellos críticos.

⚠️ Evitar:
- **PETG y PLA** — degradan con IPA repetido.
- **Goma natural** — porosa, retiene microbios.
- **Pinturas / serigrafía superficial** — se desgastan; preferir grabado por láser sobre el plástico.

### Diseño industrial sin crevices
- **Sin tornillos externos visibles** (sellados internos).
- **Esquinas redondeadas R ≥ 2 mm** para limpieza con paño.
- **Juntas tipo "labyrinth seal"** entre carcaza inferior y superior.
- **Botón táctil con membrana sellada** (no botón mecánico expuesto).
- **Indicador LED a través de la carcaza** (no LED expuesto).
- **Sensor de huella protegido por vidrio templado replaceable** (intercambiable cuando se ralla).

### Ingress Protection
- **IP65 mínimo** (protegido contra polvo + chorros de agua a baja presión).
- **IP67 ideal** (sumergible 1m / 30 min) — permite "lavar" el dispositivo entero.

### Resistencia a caídas
- **IK06** mínimo (resistencia a impactos 1 J).
- Esquinas con bumper TPU.

---

## 6. BOM detallada conectividad — escenarios

### Caso A: Solo WiFi (hospital con buena cobertura)
- ESP32-S3 standalone: $7
- **Sin SIM, sin antena LTE, sin chip celular** → ahorra $25-30.
- Riesgo: si WiFi falla, dispositivo offline.

### Caso B: Dual WiFi + LTE Cat-M1 (recomendado)
- ESP32 + módulo LTE-M (SIM7080, SIM7070): $20-30.
- LTE-M consume menos batería que LTE Cat 4.
- Cobertura M2M nacional decente.

### Caso C: WiFi + LTE Cat 4 (premium)
- LilyGo T-SIM7600 (todo integrado): $50.
- Más ancho de banda, mejor latencia.
- Mayor consumo batería.

### Caso D: WiFi + Iridium satelital (zonas remotas Patagonia / NEA / NOA)
- Módulo Iridium 9602/9603: +$200-300.
- Solo para hospitales sin LTE confiable.
- Caro pero garantiza cobertura.

### Costo conectividad mensual (~USD/mes)
- SIM Cat-M1 M2M (Personal/Movistar/Claro AR): $1-3
- SIM Cat 4 4G: $5-10
- Iridium satellite (1 KB/mes): $20-50

---

## 7. Comparativa con dispositivos COTS ya existentes

Si no querés construir hardware propio, podés usar lectores comerciales conectados a tablet:

| Solución COTS | Precio USD | Ventajas | Desventajas |
|---|---|---|---|
| **DigitalPersona 4500 + Tablet Android** | $80 lector + $200 tablet = $280 | Listo para usar, AFIP ya lo usa | Cable USB, no portátil "puro" |
| **Suprema BioMini Slim 2 + Tablet** | $200 + $200 = $400 | Mejor calidad, FBI-PIV | Idem |
| **Mobile fingerprint (Mantra MFS100 Bluetooth)** | $150 | Conexión BT, sin cable | Menor calidad |
| **iPhone con TouchID** | imposible | TouchID no expone API a terceros | Apple no permite uso third-party |
| **Smartphone Android + sensor integrado** | $300+ | Compacto | Sensor integrado no es certificable FBI |

⚠️ Recomendación: para **MVP en hospital piloto**, **DigitalPersona U.are.U 4500 + tablet Android industrial (Samsung Tab Active 5)** = **~$650 USD instalado** con cero tiempo de desarrollo hardware. Es la **opción más rápida y barata** para llegar al desafío AFIDEER.

→ **Solo construir hardware propio después de validar el caso de uso** con COTS.

---

## 8. Roadmap de hardware

| Fase | Solución hardware | Costo total instalado | Plazo |
|---|---|---|---|
| **Demo AFIDEER (nov 2026)** | Tablet Android industrial + DigitalPersona 4500 USB + cámara tablet | ~$650 USD | 1 mes |
| **Piloto hospital (2027)** | Encapsulado Tier B custom + Suprema BioMini Slim 2 + ESP32-S3 + LTE-M | ~$520 USD/unidad x 5 | 6 meses |
| **Escala (post-piloto, 2028+)** | Tier C con molde propio | ~$370 USD/unidad x 50 | 12 meses |

---

## 9. Costo total del programa piloto (estimado)

| Concepto | Cantidad | USD |
|---|---|---|
| 5 dispositivos Tier B | 5 × $520 | $2.600 |
| 5 tablets Samsung Tab Active 5 | 5 × $400 | $2.000 |
| Licencia liveness pasivo Veridas | 1 año, 1000 verifs | $300 |
| Hosting gateway DonorID (AWS / Arsat) | 1 año | $600 |
| Desarrollo software inicial | hours pro bono / estudiantes UAP | $0–10.000 |
| Convenio + certificaciones | trámites ENACOM, AAIP | $1.000 |
| **Total piloto 5 hospitales** | | **~$6.500–17.000 USD** |

### Comparación de valor
- Un solo trasplante hepático perdido por mal CIT cuesta al sistema sanitario **>USD 50.000** (costo de mantenimiento del paciente en lista + diálisis + complicaciones).
- Si el piloto evita **1 trasplante perdido**, ya se paga solo.
- **Argumento económico fuerte** para conseguir financiamiento.

---

## 10. Proveedores en Argentina

| Componente | Proveedor AR | Notas |
|---|---|---|
| Suprema BioMini | Distribuidor: SmartGroup AR, BiometricaAR | Tiempo entrega 4-6 semanas |
| DigitalPersona | Distribuidor: HID Global LatAm + Mainsoft AR | Stock local frecuente |
| ESP32 / R503 / módulos genéricos | MercadoLibre AR, Electrónica Lebot, Olimex | Stock variable, entrega rápida |
| LilyGo T-SIM7600 | Importación (Aliexpress / Amazon US) | 1-3 semanas |
| PCB fabricación | JLCPCB / PCBWay (China) | $5-50 por PCB, 1 semana shipping |
| PCB fabricación AR | Microclear, ElectronicaSA | Más caro pero soporte local |
| Encapsulado inyectado | Plásticos Lurati, Plásticos Roldan (Córdoba) | Moldes ~$3-8k |
| Encapsulado CNC | Talleres AMBA / Córdoba | $20-80 por unidad |
| Certificación ENACOM | ENACOM + lab acreditado (IRAM) | $1-3k por certificación |

---

## 11. Decisiones que el equipo debe tomar

| Decisión | Opción A | Opción B | Recomendación |
|---|---|---|---|
| Hardware MVP | Construir propio | COTS tablet + USB | **COTS para Desafío 2026** |
| Sensor certificación | FBI-PIV obligatorio | Cualquier capacitivo | **FBI-PIV mínimo** para piloto hospital |
| Conectividad | WiFi solamente | Dual WiFi + LTE | **Dual** para piloto real |
| Cargo del dispositivo | Hospital lo compra | Empresa/ONG lo presta como servicio | **Modelo SaaS hardware** (Casa Justina + sponsor) |
| Sterilization | Wipeable IP65 | Autoclavable | **IP65** (autoclave dañaría electrónica) |
| Servicio post-venta | DIY | Contrato con vendor | **Contrato con Suprema o HID** |

---

## 12. Apéndice — Especificaciones técnicas de sensores comparados

### Suprema BioMini Slim 2 (recomendado Tier B)
- Sensor: óptico
- Resolución: 500 dpi
- Área de captura: 16 × 18 mm
- Tasas: FAR < 0.001%, FRR < 1%
- Interfaz: USB 2.0
- Certificaciones: FBI-PIV, Mobile ID FAP20
- IP: IP65
- Precio: ~$180-220 USD

### DigitalPersona U.are.U 4500
- Sensor: óptico
- Resolución: 512 dpi
- Área: 14.6 × 18.1 mm
- Tasas: FAR < 0.01%, FRR ~1%
- Interfaz: USB 2.0
- Certificaciones: PIV
- IP: IP54 (declarado)
- Precio: ~$80-180 USD
- **Es el sensor que usa AFIP/ANSES** → familiaridad con personal AR.

### Grow R503 (Tier A solo)
- Sensor: capacitivo
- Resolución: 508 dpi
- Área: ⌀14 mm circular
- Tasas: FAR < 0.001%, FRR < 1% (claim del fabricante, no certificado)
- Interfaz: UART
- Certificaciones: ninguna FBI
- IP: IP67 (ring solo)
- Precio: ~$20 USD

---

## 13. Ficheros relacionados

- [01_propuesta_deteccion_facial_renaper.md](01_propuesta_deteccion_facial_renaper.md)
- [02_sid_validacion_pasiva_y_api_dispositivos_firmados.md](02_sid_validacion_pasiva_y_api_dispositivos_firmados.md)
- [../sources/18_renaper_sid_capabilities.md](../sources/18_renaper_sid_capabilities.md)
