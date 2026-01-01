# Bill of Materials (BoM) - Iran EV Charger Project

## نمای کلی

این فایل شامل لیست کامل قطعات مورد نیاز برای ساخت دو نوع شارژر است:
1. **AC Wallbox** (7.4/22 kW)
2. **DC Fast Charger** (60-120 kW)

---

# BoM - AC Wallbox Control Board (7.4/22 kW)

## 1. میکروکنترلر و پردازنده

| ردیف | Part Number | شرح | تعداد | قیمت واحد (USD) | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------------|-----|---------|---------|
| 1.1 | STM32G474RET6 | MCU 32-bit ARM Cortex-M4F, 170MHz, 512KB Flash, LQFP-64 | 1 | $8.00 | $8.00 | STMicroelectronics | Mouser, Digikey |
| 1.2 | ESP32-WROOM-32E | آلترناتیو: MCU با WiFi/BT داخلی | 1 | $3.50 | $3.50 | Espressif | AliExpress, LCSC |
| 1.3 | 25MHz Crystal | کریستال 25MHz HC-49S | 1 | $0.30 | $0.30 | Generic | Mouser |
| 1.4 | 32.768kHz Crystal | RTC Crystal | 1 | $0.20 | $0.20 | Generic | Mouser |

**انتخاب**: STM32 برای production، ESP32 برای prototype ارزان

---

## 2. CP/PP Interface Components

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 2.1 | 74LVC1G17 | Single Schmitt-Trigger Buffer (level shifter) | 2 | $0.15 | $0.30 | NXP | Mouser |
| 2.2 | IRF9540N | P-Channel MOSFET 100V 23A (CP +12V switch) | 1 | $1.20 | $1.20 | Infineon | Mouser |
| 2.3 | IRLZ44N | N-Channel MOSFET 55V 47A (CP -12V switch) | 1 | $0.80 | $0.80 | Infineon | Mouser |
| 2.4 | MBR10100CT | Schottky Diode 10A 100V (flyback protection) | 2 | $0.60 | $1.20 | OnSemi | Mouser |
| 2.5 | - | Resistor 1kΩ 1% 1/4W (CP resistor) | 2 | $0.02 | $0.04 | Generic | - |
| 2.6 | - | Resistor 100kΩ 1% (CP voltage divider) | 2 | $0.02 | $0.04 | Generic | - |
| 2.7 | - | Resistor 10kΩ 1% (CP/PP voltage divider) | 3 | $0.02 | $0.06 | Generic | - |
| 2.8 | - | Resistor 1kΩ (PP pull-up) | 1 | $0.02 | $0.02 | Generic | - |
| 2.9 | SMBJ15A | TVS Diode 15V (CP protection) | 2 | $0.25 | $0.50 | Littelfuse | Mouser |
| 2.10 | 1N4148 | Switching Diode (general purpose) | 4 | $0.05 | $0.20 | Generic | - |

---

## 3. Current Sensing

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 3.1 | SCT-013-000 | Split-core CT 0-100A (AC current sensor) | 3 | $4.50 | $13.50 | YHDC | AliExpress |
| 3.2 | ACS758LCB-100B-PFF-T | آلترناتیو: Hall Effect 0-100A (دقیق‌تر) | 3 | $12.00 | $36.00 | Allegro | Mouser |
| 3.3 | - | Burden Resistor 100Ω 1W (برای CT) | 3 | $0.10 | $0.30 | Generic | - |
| 3.4 | OPA2340 | Dual Op-amp Rail-to-Rail (signal conditioning) | 2 | $1.20 | $2.40 | TI | Mouser |
| 3.5 | - | Capacitor 10μF 50V (فیلتر) | 6 | $0.10 | $0.60 | Generic | - |
| 3.6 | SMAJ5.0A | TVS Diode 5V (ADC protection) | 3 | $0.20 | $0.60 | Littelfuse | Mouser |

**توصیه**: SCT-013 برای نسخه اقتصادی، ACS758 برای دقت بالا

---

## 4. Voltage Sensing

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 4.1 | - | Resistor 470kΩ 1W (voltage divider HV) | 3 | $0.15 | $0.45 | Generic | - |
| 4.2 | - | Resistor 4.7kΩ 1% | 3 | $0.02 | $0.06 | Generic | - |
| 4.3 | BZX84C3V6 | Zener Diode 3.6V (clamp) | 3 | $0.10 | $0.30 | NXP | Mouser |
| 4.4 | - | Capacitor 100nF 630V X2 (فیلتر) | 3 | $0.30 | $0.90 | Generic | - |

---

## 5. RCM Type B Module

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 5.1 | RCMB121 | RCM Type B module 30mA (Bender) | 1 | $120.00 | $120.00 | Bender | Bender Direct |
| 5.2 | RCCB-3030-B | آلترناتیو ارزان‌تر (کیفیت کمتر) | 1 | $60.00 | $60.00 | Generic | AliExpress |
| 5.3 | TLP181 | Optocoupler (fault signal isolation) | 1 | $0.40 | $0.40 | Toshiba | Mouser |

---

## 6. Temperature Sensors

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 6.1 | PT1000 | Platinum RTD sensor 1000Ω @ 0°C (connector temp) | 1 | $5.00 | $5.00 | Heraeus | Mouser |
| 6.2 | NTCLE100E3103JB0 | NTC 10kΩ B3950 (board temp) | 2 | $0.20 | $0.40 | Vishay | Mouser |
| 6.3 | LM75ADP | Digital temp sensor I2C (ambient) | 1 | $0.80 | $0.80 | NXP | Mouser |
| 6.4 | - | Resistor 10kΩ 1% (NTC ref) | 2 | $0.02 | $0.04 | Generic | - |

---

## 7. Contactor & Relay

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 7.1 | EV200AAANA | TE Connectivity EV Contactor 40A (تک‌فاز) | 1 | $38.00 | $38.00 | TE Connectivity | Mouser |
| 7.2 | KILOVAC-EV200 | آلترناتیو: 100A 3-pole (سه‌فاز) | 1 | $65.00 | $65.00 | TE Connectivity | Mouser |
| 7.3 | G3VM-61A1 | SSR MOSFET relay (کمکی) | 2 | $2.50 | $5.00 | Omron | Mouser |
| 7.4 | TLP181 | Optocoupler (contactor drive isolation) | 2 | $0.40 | $0.80 | Toshiba | Mouser |
| 7.5 | IRLZ44N | MOSFET N-Ch (contactor coil driver) | 2 | $0.80 | $1.60 | Infineon | Mouser |
| 7.6 | 1N4007 | Diode 1A 1000V (flyback) | 2 | $0.05 | $0.10 | Generic | - |

---

## 8. HMI Components

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 8.1 | SSD1306 | OLED Display 0.96" 128x64 I2C | 1 | $3.50 | $3.50 | Generic | AliExpress |
| 8.2 | WS2812B-2020 | RGB LED (status indicator) | 5 | $0.15 | $0.75 | Worldsemi | LCSC |
| 8.3 | MFRC522 | RFID Reader 13.56MHz | 1 | $2.50 | $2.50 | NXP | AliExpress |
| 8.4 | KSS-241GLFS | Buzzer 12V (alert) | 1 | $1.20 | $1.20 | Kingstate | Mouser |
| 8.5 | - | Push button tactile 6x6mm | 3 | $0.10 | $0.30 | Generic | - |

---

## 9. Communication Modules

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 9.1 | W5500 | SPI to Ethernet controller | 1 | $4.50 | $4.50 | WIZnet | Mouser |
| 9.2 | HR911105A | RJ45 connector with magnetics | 1 | $0.80 | $0.80 | Hanrun | LCSC |
| 9.3 | SIM7600E-H | 4G LTE module (برای ایران) | 1 | $32.00 | $32.00 | SIMCom | AliExpress |
| 9.4 | - | SIM card holder 6-pin | 1 | $0.50 | $0.50 | Generic | LCSC |
| 9.5 | - | 4G Antenna 3dBi with SMA | 1 | $2.00 | $2.00 | Generic | AliExpress |
| 9.6 | TJA1050 | CAN transceiver (future expansion) | 1 | $0.60 | $0.60 | NXP | Mouser |

---

## 10. Power Supply

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 10.1 | HLK-PM12 | AC-DC 220V to 12V 1A module | 1 | $3.50 | $3.50 | Hi-Link | AliExpress |
| 10.2 | IRM-20-12 | آلترناتیو: Mean Well 12V 1.67A (ایمن‌تر) | 1 | $7.50 | $7.50 | Mean Well | Mouser |
| 10.3 | LM2596S-ADJ | Buck converter 12V→5V 3A | 1 | $0.80 | $0.80 | TI | LCSC |
| 10.4 | AMS1117-3.3 | LDO 5V→3.3V 1A | 1 | $0.15 | $0.15 | AMS | LCSC |
| 10.5 | ICL7660SCPA | Charge pump +12V→-12V | 1 | $1.20 | $1.20 | Renesas | Mouser |
| 10.6 | - | Inductor 47μH 3A (برای buck) | 1 | $0.30 | $0.30 | Generic | - |
| 10.7 | - | Schottky diode SS34 | 2 | $0.10 | $0.20 | Generic | - |
| 10.8 | - | Electrolytic cap 470μF 35V | 5 | $0.20 | $1.00 | Generic | - |
| 10.9 | - | Ceramic cap 100μF 16V X7R | 10 | $0.30 | $3.00 | Generic | - |

---

## 11. Protection Components

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 11.1 | S14K275 | MOV Varistor 275V (SPD) | 3 | $0.40 | $1.20 | Littelfuse | Mouser |
| 11.2 | GDT230-R | Gas Discharge Tube 230V | 2 | $0.80 | $1.60 | Bourns | Mouser |
| 11.3 | - | Fuse 250V 2A slow-blow (input) | 1 | $0.30 | $0.30 | Generic | - |
| 11.4 | - | Fuse holder PCB mount | 1 | $0.40 | $0.40 | Generic | - |

---

## 12. Passive Components (Summary)

| ردیف | شرح | تعداد | قیمت کل |
|------|-----|-------|---------|
| 12.1 | Resistors 0805 (assorted values) | ~100 | $2.00 |
| 12.2 | Ceramic capacitors 0805 (0.1μF, 1μF, etc.) | ~80 | $3.00 |
| 12.3 | Electrolytic capacitors (assorted) | ~20 | $2.00 |
| 12.4 | Diodes (1N4148, 1N4007, etc.) | ~15 | $0.50 |

---

## 13. Connectors & Mechanical

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 13.1 | - | Terminal block 3-pin 5mm (AC input) | 1 | $0.60 | $0.60 | Phoenix | Mouser |
| 13.2 | - | Header 2.54mm 2-pin (CP/PP output) | 2 | $0.10 | $0.20 | Generic | - |
| 13.3 | JST-XH-3P | JST connector 3-pin (sensors) | 5 | $0.15 | $0.75 | JST | LCSC |
| 13.4 | - | SWD programming header 1.27mm 10-pin | 1 | $0.30 | $0.30 | Generic | - |
| 13.5 | - | Spacer M3 10mm (PCB mounting) | 4 | $0.10 | $0.40 | Generic | - |
| 13.6 | - | Screw M3x6mm | 8 | $0.05 | $0.40 | Generic | - |

---

## 14. PCB Manufacturing

| ردیف | شرح | قیمت (qty 10 pcs) |
|------|-----|-------------------|
| 14.1 | PCB 4-layer, 150x100mm, 1.6mm, HASL, Green | $80.00 |
| 14.2 | Assembly (SMD stencil + pick-place) | اختیاری |

**فروشندگان پیشنهادی**: JLCPCB، PCBWay، AllPCB

---

## 15. Enclosure & Final Assembly

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|
| 15.1 | - | ABS/Metal enclosure IP54 (250x200x80mm) | 1 | $18.00 | $18.00 | AliExpress |
| 15.2 | - | Type 2 socket (IEC 62196-2) | 1 | $45.00 | $45.00 | Phoenix Contact |
| 15.3 | - | Type 2 cable assembly 5m 32A | آلترناتیو | $85.00 | $85.00 | Phoenix Contact |
| 15.4 | - | Cable gland M20 | 2 | $1.50 | $3.00 | Generic |

---

# جمع هزینه AC Wallbox

| بخش | هزینه (USD) |
|-----|-------------|
| PCB + Assembly | $80 |
| Electronic Components | $130 |
| RCM Module | $120 |
| Contactor | $38-65 |
| Communication Modules | $40 |
| Enclosure + Connectors | $65-110 |
| **جمع کل (تک‌فاز 7.4kW)** | **$473-545** |
| **جمع کل (سه‌فاز 22kW)** | **$500-572** |

*توجه: قیمت‌ها برای خرید تکی است. در خرید bulk (100+ واحد) می‌توان 30-40% کاهش داد.*

---

---

# BoM - DC Fast Charger Control Board (60-120 kW)

## 1. Processing Units

| ردیف | Part Number | شرح | تعداد | قیمت واحد (USD) | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------------|-----|---------|---------|
| 1.1 | STM32F765VIT6 | Safety MCU, Cortex-M7 216MHz, LQFP-100 | 1 | $22.00 | $22.00 | STMicro | Mouser |
| 1.2 | MCIMX8M-MINI | i.MX8M Mini SoM module (با carrier board) | 1 | $180.00 | $180.00 | NXP | Mouser |
| 1.3 | STM32MP157F | آلترناتیو ارزان‌تر: Dual A7 + M4 | 1 | $85.00 | $85.00 | STMicro | Mouser |
| 1.4 | 25MHz Crystal | برای STM32F7 | 1 | $0.30 | $0.30 | Generic | - |
| 1.5 | 32.768kHz Crystal | RTC | 1 | $0.20 | $0.20 | Generic | - |

**انتخاب**: i.MX8M برای production، STM32MP1 برای بودجه محدود

---

## 2. PLC Communication (ISO 15118)

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 2.1 | QCA7005-AL31 | Green PHY PLC modem IC | 1 | $12.00 | $12.00 | Qualcomm | Mouser |
| 2.2 | QCA7005-EVB | آلترناتیو: QCA7005 evaluation module آماده | 1 | $45.00 | $45.00 | Qualcomm | Direct |
| 2.3 | - | Coupling transformer 1:1 (PLC) | 1 | $3.00 | $3.00 | Custom | - |
| 2.4 | - | Capacitor 1μF 1kV X2 (DC block) | 2 | $0.80 | $1.60 | WIMA | Mouser |
| 2.5 | - | Common-mode choke 10mH | 1 | $2.50 | $2.50 | Wurth | Mouser |
| 2.6 | GDT230-R | GDT 230V (PLC line protection) | 1 | $0.80 | $0.80 | Bourns | Mouser |

**نرم‌افزار ISO 15118 Stack:**
- **ChargePoint ISO 15118**: ~$2000 (یکبار، license)
- **Pionix EVerest**: رایگان (open-source اما نیاز به تخصیص)
- **Vector EVSE Stack**: ~$1500-3000

---

## 3. Insulation Monitoring (IMD)

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 3.1 | isoPV800 | Bender IMD module for EV (up to 1000V) | 1 | $380.00 | $380.00 | Bender | Bender Direct |
| 3.2 | IR155-3204 | آلترناتیف: Bender IR155 series | 1 | $320.00 | $320.00 | Bender | Mouser |
| 3.3 | TLP181 | Optocoupler (IMD signal isolation) | 1 | $0.40 | $0.40 | Toshiba | Mouser |

---

## 4. DC Leakage Detection

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 4.1 | RCMB-DC | Bender DC leakage module 6mA | 1 | $340.00 | $340.00 | Bender | Bender Direct |
| 4.2 | - | Current transformer DC-sensitive | 1 | $80.00 | $80.00 | Custom | AliExpress |

---

## 5. DC Current Sensing (High Accuracy)

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 5.1 | HTFS 400-P/SP1 | LEM Hall sensor 0-400A (output DC) | 2 | $120.00 | $240.00 | LEM | Mouser |
| 5.2 | DS200 | آلترناتیف: Danisense fluxgate 200A (دقیق‌تر) | 2 | $180.00 | $360.00 | Danisense | Direct |
| 5.3 | AMC1301 | Isolation amplifier (sensor to ADC) | 2 | $3.50 | $7.00 | TI | Mouser |
| 5.4 | ADS8688 | 16-bit ADC 8-channel (high precision) | 1 | $12.00 | $12.00 | TI | Mouser |

**انتخاب**: LEM برای cost-effective، Danisense برای metering دقیق

---

## 6. DC Voltage Sensing

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 6.1 | DV 1000-P | LEM voltage transducer 0-1000V | 2 | $55.00 | $110.00 | LEM | Mouser |
| 6.2 | - | Resistor 1MΩ 5W high voltage | 4 | $1.50 | $6.00 | Vishay | Mouser |
| 6.3 | S20K750 | MOV 750V (overvoltage protection) | 2 | $0.80 | $1.60 | Littelfuse | Mouser |
| 6.4 | AMC1301 | Isolation amplifier | 2 | $3.50 | $7.00 | TI | Mouser |

---

## 7. DC Contactors

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 7.1 | EVC500 | TE Connectivity 1000V 500A DC contactor | 3 | $145.00 | $435.00 | TE Connectivity | Mouser |
| 7.2 | SW200 | Gigavac SW200 (آلترناتیو) | 3 | $160.00 | $480.00 | Gigavac | Direct |
| 7.3 | TLP181 | Optocoupler (drive isolation) | 3 | $0.40 | $1.20 | Toshiba | Mouser |
| 7.4 | IRLZ44N | MOSFET driver | 3 | $0.80 | $2.40 | Infineon | Mouser |
| 7.5 | 1N4007 | Flyback diode | 3 | $0.05 | $0.15 | Generic | - |

---

## 8. Pre-charge Circuit

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 8.1 | - | Pre-charge resistor 100Ω 100W wirewound | 1 | $12.00 | $12.00 | TE Connectivity | Mouser |
| 8.2 | - | Bleeder resistor 10kΩ 50W (discharge) | 2 | $5.00 | $10.00 | Vishay | Mouser |

---

## 9. Temperature Sensors

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 9.1 | PT1000 | RTD sensor (connector pins) | 4 | $5.00 | $20.00 | Heraeus | Mouser |
| 9.2 | PT1000 | RTD sensor (power modules) | 4 | $5.00 | $20.00 | Heraeus | Mouser |
| 9.3 | MAX31865 | RTD to digital converter SPI | 2 | $7.50 | $15.00 | Maxim | Mouser |
| 9.4 | LM75A | Ambient temp sensor I2C | 2 | $0.80 | $1.60 | NXP | Mouser |

---

## 10. HMI Components

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 10.1 | - | 7" TFT LCD 800x480 capacitive touch | 1 | $75.00 | $75.00 | Generic | AliExpress |
| 10.2 | - | MIPI DSI to LVDS converter (اگر نیاز) | 1 | $8.00 | $8.00 | TI | Mouser |
| 10.3 | MFRC522 | RFID reader 13.56MHz | 1 | $2.50 | $2.50 | NXP | AliExpress |
| 10.4 | WS2812B | RGB LED strip (status) | 10 | $0.15 | $1.50 | Worldsemi | LCSC |
| 10.5 | - | Push button E-Stop 40mm mushroom | 1 | $15.00 | $15.00 | Schneider | Mouser |
| 10.6 | - | Emergency stop NC contact block | 1 | $8.00 | $8.00 | Schneider | Mouser |

---

## 11. Communication Modules

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 11.1 | - | Gigabit Ethernet PHY (معمولاً داخل SoM) | - | - | - | - | - |
| 11.2 | - | RJ45 jack با magnetics | 1 | $1.50 | $1.50 | Hanrun | LCSC |
| 11.3 | SIM7600E-H | 4G LTE module | 1 | $32.00 | $32.00 | SIMCom | AliExpress |
| 11.4 | - | 4G antenna 5dBi | 2 | $3.00 | $6.00 | Generic | AliExpress |
| 11.5 | TJA1043 | CAN-FD transceiver | 3 | $1.20 | $3.60 | NXP | Mouser |

---

## 12. Power Supply Modules

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 12.1 | RPA60-4805SW/P | RECOM 800V→48V 60W isolated | 1 | $85.00 | $85.00 | RECOM | Mouser |
| 12.2 | TSR-3-2450 | TRACO 48V→24V 3A buck | 1 | $18.00 | $18.00 | TRACO | Mouser |
| 12.3 | LM2576HVS-12 | 48V→12V 3A buck converter | 1 | $3.50 | $3.50 | TI | Mouser |
| 12.4 | LM2596S-5.0 | 12V→5V 3A buck | 1 | $0.80 | $0.80 | TI | LCSC |
| 12.5 | AMS1117-3.3 | 5V→3.3V LDO 1A | 2 | $0.15 | $0.30 | AMS | LCSC |
| 12.6 | - | Battery backup 12V 7Ah SLA | 1 | $18.00 | $18.00 | Generic | - |

---

## 13. Protection & Safety

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 13.1 | S20K750 | MOV 750V | 4 | $0.80 | $3.20 | Littelfuse | Mouser |
| 13.2 | GDT750-R | Gas discharge tube 750V | 2 | $1.50 | $3.00 | Bourns | Mouser |
| 13.3 | - | Fuse 1000V 400A DC | 2 | $12.00 | $24.00 | Bussmann | Mouser |
| 13.4 | - | Thermal switch N.O. 85°C | 4 | $2.50 | $10.00 | Sensata | Mouser |

---

## 14. Passive Components (Summary)

| ردیف | شرح | تعداد | قیمت کل |
|------|-----|-------|---------|
| 14.1 | Resistors 0805/1206 (assorted) | ~200 | $5.00 |
| 14.2 | Ceramic caps 0805/1206 | ~150 | $8.00 |
| 14.3 | Electrolytic caps (high voltage) | ~40 | $15.00 |
| 14.4 | Inductors & chokes | ~10 | $10.00 |
| 14.5 | Diodes & rectifiers | ~30 | $3.00 |

---

## 15. Connectors & Mechanical

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 15.1 | - | Anderson PowerPole 350A (DC input) | 2 | $15.00 | $30.00 | Anderson | Mouser |
| 15.2 | - | Terminal block 25mm² (AC input) | 1 | $8.00 | $8.00 | Phoenix | Mouser |
| 15.3 | - | Molex Mega-Fit 6-pin (power modules) | 4 | $3.50 | $14.00 | Molex | Mouser |
| 15.4 | - | Phoenix COMBICON 5-pin (CAN) | 3 | $2.00 | $6.00 | Phoenix | Mouser |
| 15.5 | - | Tag-Connect TC2050 (programming) | 1 | $8.00 | $8.00 | Tag-Connect | Mouser |
| 15.6 | JST-XH series | Sensor connectors (assorted) | 15 | $0.15 | $2.25 | JST | LCSC |

---

## 16. CCS2 Connector & Cable

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 16.1 | - | CCS2 connector plug (IEC 62196-3) | 1 | $450.00 | $450.00 | Phoenix Contact | Direct |
| 16.2 | - | DC cable 50mm² x2, 5m (200A rated) | 1 | $120.00 | $120.00 | Generic | - |
| 16.3 | - | Liquid-cooled cable (برای >250A) | 1 | $350.00 | $350.00 | Huber+Suhner | Direct |

**انتخاب**: کابل معمولی برای 60-80kW، خنک‌شونده برای 120kW

---

## 17. Power Modules (Main Cost!)

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | سازنده | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|---------|
| 17.1 | - | DC/DC module 20kW 800V→200-750V isolated | 3 | $1,800 | $5,400 | Delta/Huawei | Direct |
| 17.2 | - | آلترناتیو: 30kW module | 3 | $2,400 | $7,200 | Infineon | Direct |
| 17.3 | - | PFC module Vienna rectifier 60kW | 1 | $1,200 | $1,200 | Custom | - |

**برای 120kW**: 4x 30kW module = $9,600

---

## 18. Enclosure & Cooling

| ردیف | Part Number | شرح | تعداد | قیمت واحد | جمع | فروشنده |
|------|-------------|-----|-------|-----------|-----|---------|
| 18.1 | - | Steel/Aluminum enclosure IP54 (800x500x300mm) | 1 | $280.00 | $280.00 | Custom |
| 18.2 | - | Fan 120mm 48V DC 200CFM | 3 | $25.00 | $75.00 | ebm-papst | Mouser |
| 18.3 | - | Heatsink for power modules (custom) | 3 | $40.00 | $120.00 | Custom | - |
| 18.4 | - | Air filter | 2 | $8.00 | $16.00 | Generic | - |

---

## 19. PCB Manufacturing

| ردیف | شرح | قیمت (qty 10 pcs) |
|------|-----|-------------------|
| 19.1 | Control board 6-layer 300x200mm 2mm 3oz | $150.00 |
| 19.2 | Carrier board for SoM (4-layer) | $60.00 |
| 19.3 | SMD stencil | $20.00 |

---

# جمع هزینه DC Fast Charger

| بخش | هزینه (USD) |
|-----|-------------|
| **Control Electronics** | |
| - MCU & SoM | $200-220 |
| - PLC Module | $60 |
| - IMD + Leakage Detection | $720 |
| - Current/Voltage Sensors | $370 |
| - Contactors | $435-480 |
| - Pre-charge & Discharge | $22 |
| - Temperature Sensors | $60 |
| - HMI (Display + RFID) | $105 |
| - Communication | $45 |
| - Power Supply | $125 |
| - Protection | $40 |
| - Passives & Connectors | $70 |
| - PCB Manufacturing | $230 |
| **Subtotal Control** | **~$2,480** |
| | |
| **Power Stage** | |
| - Power Modules (3x20kW) | $5,400 |
| - PFC Module | $1,200 |
| **Subtotal Power** | **$6,600** |
| | |
| **Mechanical** | |
| - CCS2 Connector + Cable | $570-920 |
| - Enclosure + Cooling | $490 |
| **Subtotal Mechanical** | **$1,060-1,410** |
| | |
| **Software** | |
| - ISO 15118 Stack License | $1,500-2,000 |
| | |
| **جمع کل (60kW)** | **~$11,640-12,490** |
| **جمع کل (120kW)** | **~$15,240-16,090** |

*توجه: این هزینه برای نمونه اولیه است. در تولید انبوه (100+ واحد) با تخفیف bulk می‌توان 25-35% کاهش داد.*

---

# مقایسه هزینه

| محصول | هزینه Control Board | هزینه کل |
|-------|---------------------|----------|
| **AC Wallbox 7.4kW** | $200-250 | $470-545 |
| **AC Wallbox 22kW** | $230-280 | $500-572 |
| **DC Fast 60kW** | $2,480 | $11,600-12,500 |
| **DC Fast 120kW** | $2,480 | $15,200-16,100 |

---

# توصیه‌های خرید

## برای Prototype (نمونه اولیه)

1. **خرید از Mouser/Digikey**: قطعات کیفیت بالا، سریع
2. **AliExpress/LCSC**: قطعات generic، صرفه‌جویی 30-50%
3. **Bender Direct**: ماژول‌های ایمنی (IMD, RCM)

## برای Production (تولید انبوه)

1. **LCSC/JLC**: خرید bulk با تخفیف
2. **ارتباط مستقیم با سازندگان**: Delta، Huawei (power modules)
3. **Manufacturing در چین**: PCB + Assembly یکجا

## تأمین‌کنندگان ایران

- **الکترونیک‌های قدرت**: بازار جمهوری/فردوسی تهران
- **Contactors صنعتی**: نمایندگی ABB، Schneider
- **ماژول‌های آماده**: واردات از چین

---

**نسخه**: 1.0
**تاریخ**: 2026-01-01
**مؤلف**: Claude AI
**پروژه**: Iran EV Charger
