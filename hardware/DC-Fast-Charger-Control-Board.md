# DC Fast Charger Control Board Design (60-120 kW)

## نمای کلی
برد کنترل برای شارژر DC سریع با قابلیت 60 kW (قابل ارتقا به 120 kW) با کانکتور CCS2 مطابق با استانداردهای IEC 61851-23/24 و ISO 15118

## معماری سخت‌افزاری - Dual MCU Architecture

### معماری کلی سیستم
```
┌────────────────────────────────────────────────────────────┐
│                    DC Fast Charger                         │
├────────────────────────────────────────────────────────────┤
│  ┌──────────────┐         ┌────────────────────────────┐  │
│  │ Safety MCU   │◄───────►│   Main SoM                 │  │
│  │ (STM32F7)    │  CAN    │   (i.MX8M / STM32MP1)      │  │
│  │              │         │   - OCPP 2.0.1             │  │
│  │ - Pre-charge │         │   - HMI / Display          │  │
│  │ - Contactors │         │   - WiFi/4G/Ethernet       │  │
│  │ - Power Ctrl │         │   - Backend Comm           │  │
│  │ - IMD/RISO   │         │   - RFID / Payment         │  │
│  │ - Emergency  │         └────────────────────────────┘  │
│  └──────────────┘                                          │
│         ↕                                                  │
│  ┌──────────────────────────────────────────────────────┐ │
│  │         PLC Communication Module                     │ │
│  │         (Green PHY + ISO 15118 Stack)                │ │
│  └──────────────────────────────────────────────────────┘ │
│         ↕                                                  │
│  ┌──────────────────────────────────────────────────────┐ │
│  │    Power Modules (3-4x 20-30kW DC/DC stackable)     │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

---

## 1. Safety MCU (میکروکنترلر ایمنی)

### 1.1 انتخاب MCU: STM32F765VIT6

**چرا STM32F7 Series؟**
- **هسته**: ARM Cortex-M7 @ 216 MHz (با FPU)
- **حافظه**: 2 MB Flash، 512 KB RAM
- **Safety Features**:
  - Dual watchdog (independent + window)
  - ECC on Flash/RAM
  - Memory Protection Unit (MPU)
  - CRC calculation unit
- **Timers**: High-resolution timers برای کنترل دقیق
- **ADC**: 3x 12-bit SAR (2.4 Msps)
- **Communication**:
  - 3x CAN-FD (برای ارتباط با ماژول‌های قدرت)
  - 2x SPI (برای سنسورها)
  - 4x UART
  - 2x I2C
- **پکیج**: LQFP-100
- **گواهینامه**: ASIL-B capable

### 1.2 وظایف Safety MCU

1. **Pre-charge Sequence**
   - کنترل کنتاکتور pre-charge
   - مانیتور ولتاژ capacitor bank
   - Soft-start برای جلوگیری از inrush

2. **Contactor Management**
   - کنتاکتور اصلی DC+ و DC-
   - کنتاکتور pre-charge
   - تأیید وضعیت (welding detection)

3. **Insulation Monitoring (IMD/RISO)**
   - مانیتور مقاومت عایق بین DC و زمین
   - آستانه: >100Ω/V (مثلاً >75kΩ برای 750V)

4. **DC Leakage Detection**
   - تشخیص نشتی جریان DC
   - قطع سریع در صورت خطر

5. **Emergency Stop**
   - دکمه قطع اضطراری سخت‌افزاری
   - قطع فوری تمام کنتاکتورها

6. **Temperature Monitoring**
   - دمای ماژول‌های قدرت
   - دمای کانکتور CCS2
   - دمای کابل (اگر خنک‌شونده)

7. **Voltage/Current Limits**
   - جلوگیری از Overvoltage/Overcurrent
   - Protection limits سخت‌افزاری

---

## 2. Main SoM (System on Module)

### 2.1 انتخاب SoM: i.MX8M Mini یا STM32MP157

**Option 1: NXP i.MX8M Mini (پیشنهاد برای production)**
- **هسته**: Quad Cortex-A53 @ 1.8GHz + Cortex-M4 @ 400MHz
- **RAM**: 1-4 GB LPDDR4
- **Storage**: eMMC 8-64 GB
- **Graphics**: GPU برای نمایشگر رنگی
- **OS**: Linux (Yocto/Debian)
- **Network**:
  - Gigabit Ethernet (OCPP WebSocket)
  - WiFi/BT (via module)
  - 4G/LTE (via USB modem)
- **Interfaces**:
  - MIPI DSI (display)
  - USB 2.0 (RFID, payment terminal)
  - CAN (ارتباط با Safety MCU)
  - I2C, SPI, UART

**Option 2: STM32MP157F (آلترناتیو کم‌هزینه)**
- **هسته**: Dual Cortex-A7 @ 650MHz + Cortex-M4 @ 209MHz
- **RAM**: 512 MB DDR3L
- **OS**: Linux + FreeRTOS
- **مزیت**: همان اکوسیستم STM32
- **معایب**: performance کمتر برای HMI

### 2.2 وظایف Main SoM

1. **OCPP Client**
   - OCPP 1.6J (حداقل) یا 2.0.1
   - WebSocket over Ethernet/4G
   - مدیریت تراکنش‌ها
   - Remote monitoring & control

2. **HMI (Human Machine Interface)**
   - نمایشگر 7" تاچ (800x480)
   - نمایش: جریان، ولتاژ، توان، انرژی، زمان، قیمت
   - User authentication (RFID/QR code)

3. **Payment Integration**
   - RFID card (MIFARE)
   - QR code payment (local wallets)
   - USSD (برای ایران)
   - Credit card terminal (اختیاری)

4. **Data Logging**
   - ذخیره تراکنش‌ها
   - لاگ خطاها
   - آمار استفاده
   - Firmware update OTA

5. **Communication Orchestration**
   - ارتباط با Safety MCU (CAN)
   - ارتباط با PLC module (Ethernet/SPI)
   - Backend connectivity

---

## 3. PLC Communication Module (CCS2/ISO 15118)

### 3.1 معماری PLC

**روش توصیه‌شده: ماژول آماده + Stack نرم‌افزاری**

#### ماژول سخت‌افزاری PLC

**Option 1: Qualcomm QCA7000/QCA7005 (Green PHY)**
- استاندارد HomePlug Green PHY
- فرکانس: 2-28 MHz (CENELEC-A band)
- Interface: SPI/UART به Main SoM
- نرخ داده: تا 10 Mbps

**Option 2: Broadcom BCM60321**
- مخصوص EVSE PLC
- داخلی‌ترین پردازش
- Interface: Ethernet

**مدار اتصال PLC:**
```
CP Line (از کانکتور CCS2) ──┬── Coupling Circuit ── PLC Modem
                              │
                              └── DC Block Cap (1μF/1kV)
                                  │
                                  ├── Common-mode choke
                                  └── Protection (GDT + TVS)
```

**Coupling Circuit:**
- Capacitor: 1μF 1kV (X2 class) - DC blocking
- Transformer: 1:1 isolation transformer
- Protection: Gas Discharge Tube + TVS diodes

### 3.2 ISO 15118 Software Stack

**Stack‌های آماده تجاری:**
- **Vector EVSE ISO 15118**: Stack کامل با certification
- **Pionix EVerest**: Open-source (رایگان اما نیاز به تخصیص)
- **CharIN Tested**: Stack‌های certify شده

**فیچرهای ISO 15118:**
- Plug & Charge (PnC) - احراز هویت خودکار
- Bidirectional charging (V2G) - اختیاری
- Dynamic pricing
- Smart charging

**پشتیبانی از DIN 70121 (legacy):**
- برای خودروهای قدیمی‌تر
- پروتکل ساده‌تر
- Fallback در صورت عدم پشتیبانی ISO 15118

---

## 4. Power Stage Interface

### 4.1 معماری ماژول‌های قدرت

**Modular Design:**
```
AC 400V 3-Phase ──→ [PFC Module] ──→ DC Bus ~800V
                                          │
        ┌─────────────────────────────────┴─────────────────┐
        │                                                     │
   [DC/DC Module 1]  [DC/DC Module 2]  [DC/DC Module 3]  [Module 4]
   20-30 kW          20-30 kW          20-30 kW          (optional)
        │                 │                 │                 │
        └─────────────────┴─────────────────┴─────────────────┘
                                  │
                            200-750V DC Output → CCS2
```

**ماژول‌های قدرت stackable:**
- هر ماژول: 20-30 kW
- 60 kW: 3 ماژول
- 90 kW: 3 ماژول (overrated)
- 120 kW: 4 ماژول

**ماژول‌های پیشنهادی:**
- Delta Q Series
- Huawei iCharger modules
- Infineon HybridPACK Drive

### 4.2 کنترل ماژول‌های قدرت

**Interface CAN Bus:**
```
Safety MCU ──CAN Bus──┬── Power Module 1 (Address 0x01)
                      ├── Power Module 2 (Address 0x02)
                      ├── Power Module 3 (Address 0x03)
                      └── Power Module 4 (Address 0x04)
```

**دستورات CAN:**
- Enable/Disable
- Set Voltage (200-750V)
- Set Current (0-400A total)
- Read Status (V, I, Temp, Faults)
- Emergency stop

**کنترل Parallel:**
- Current sharing بین ماژول‌ها
- Voltage droop compensation
- Hot-swap capability (در صورت fault یک ماژول)

---

## 5. DC Measurement & Sensing

### 5.1 اندازه‌گیری ولتاژ DC

**محدوده:** 200-950V DC

**روش 1: Voltage Divider با Isolation Amplifier**
```
DC+ ──┬── R1 (1MΩ high voltage) ──┬── R2 (10kΩ) ── GND
      │                            │
      │                            └── Isolation Amplifier ─── ADC
      └── Protection (MOV + GDT)       (AMC1301/ISO124)
```

**روش 2: Hall Effect Voltage Sensor**
- سنسور: LEM DV series (0-1000V)
- ایزوله کامل
- دقت: ±0.5%
- پاسخ سریع

**Dual Measurement:**
- ولتاژ DC Bus (800V)
- ولتاژ Output (200-750V)
- Differential measurement برای دقت

### 5.2 اندازه‌گیری جریان DC

**محدوده:** 0-400A (برای 120kW @ 300V)

**Hall Effect Current Sensor:**
- سنسور پیشنهادی: LEM HTFS 400-P/SP1
  - محدوده: ±400A
  - دقت: ±0.5%
  - پاسخ: <1μs
  - ایزوله: 3kV
  - خروجی: آنالوگ یا دیجیتال (SPI)

**Fluxgate Current Sensor (دقت بالاتر):**
- Danisense DS series
- دقت: ±0.01%
- برای metering دقیق

**مدار Conditioning:**
```
LEM Sensor Output ─── RC Filter ─── Isolation Amp ─── ADC (16-bit)
                         │
                         └── Calibration offset
```

**نرم‌افزار:**
- Oversampling 100 kHz → decimation به 10 kHz
- Digital filtering (Butterworth low-pass)
- Calibration با offset/gain در EEPROM
- Current ramping: max 10A/s برای safety

---

## 6. Insulation Monitoring Device (IMD)

### 6.1 ماژول IMD آماده

**پیشنهاد: Bender isoPV800 series**
- ولتاژ سیستم: تا 1000V DC
- حساسیت: تنظیم‌پذیر (معمولاً >100Ω/V)
- زمان پاسخ: <2 seconds
- خروجی: رله + analog signal
- استاندارد: IEC 61557-8

**اتصال:**
```
DC+ ────┐
        ├──→ [IMD Module] ──→ Fault Relay ──→ Safety MCU
DC- ────┤                       │
        │                       └── Analog Output (R value)
PE ─────┘
```

### 6.2 عملکرد IMD

**تست مقاومت عایق:**
1. قبل از بسته‌شدن کنتاکتورها → RISO باید >100Ω/V باشد
2. در حین شارژ → مانیتور مداوم
3. اگر RISO < threshold → فوری قطع

**خطاها:**
- Warning: 100-50 Ω/V
- Critical: <50 Ω/V → Emergency stop

---

## 7. DC Leakage Detection

**روش: RCM DC-sensitive**

**ماژول: Bender RCMB-DC**
- تشخیص نشتی AC و DC
- حساسیت DC: 6mA
- زمان trip: <100ms
- خروجی: رله NO/NC

**نصب:**
```
DC Output ──→ [Leakage Sensor Ring] ──→ به کابل CCS2
                     │
                     └── Signal ──→ Safety MCU
```

---

## 8. Contactor Control & Pre-charge

### 8.1 کنتاکتورهای DC

**Main Contactors:**
- **مدل پیشنهادی**: TE Connectivity EVC500
  - ولتاژ: 1000V DC
  - جریان: 500A continuous
  - کویل: 12V DC (low power hold)
  - Auxiliary contacts: برای feedback

**تعداد کنتاکتورها:**
- **K1**: DC+ Main
- **K2**: DC- Main
- **K3**: Pre-charge (DC+)

### 8.2 Pre-charge Circuit

**نقشه:**
```
DC Bus + ──┬── K3 ── R_precharge (100Ω 100W) ──┬── Output +
           │                                    │
           └── K1 ──────────────────────────────┘

DC Bus - ────── K2 ──────────────────────────────── Output -
```

**Pre-charge Resistor:**
- مقاومت: 100Ω
- توان: 100W wirewound
- محاسبه: برای شارژ 1mF از 0→800V در ~0.5s

**Sequence:**
```
1. همه کنتاکتورها باز
2. IMD check → OK
3. بستن K2 (DC-)
4. بستن K3 (pre-charge) → شارژ آهسته خازن‌های خودرو
5. Wait: ولتاژ output به 90% DC bus برسد
6. بستن K1 (main DC+)
7. باز کردن K3 (pre-charge bypass)
8. شروع شارژ
```

**مانیتورینگ:**
- اگر ولتاژ output به 90% نرسد در <2s → خطا (short circuit)
- Welding detection: اگر ولتاژ output ≠ 0 با کنتاکتور باز → welded!

### 8.3 Contactor Driver

**مدار درایور:**
```
Safety MCU GPIO ── Optocoupler ── MOSFET ──┬── Contactor Coil (+12V)
                   (TLP181)      (IRLZ44)  │
                                            └── Flyback Diode (DO-41)

Feedback: Aux Contact ── Optocoupler ── MCU GPIO
```

---

## 9. Temperature Monitoring

### 9.1 سنسورهای دما

**1) دمای ماژول‌های قدرت:**
- سنسور: PT1000 یا NTC 10kΩ (هر ماژول)
- آستانه: >80°C warning, >90°C shutdown

**2) دمای کانکتور CCS2:**
- سنسور: PT1000 تعبیه‌شده در پین‌های کانکتور
- آستانه: >70°C derating, >85°C stop

**3) دمای کابل (برای >100A):**
- سنسور: PT1000 در کابل
- کابل خنک‌شونده: مانیتور دمای مایع + flow

**4) دمای محیط:**
- سنسور: LM75A (I2C)
- کنترل فن‌ها

### 9.2 Thermal Management

**فن‌های خنک‌کننده:**
- برای ماژول‌های قدرت: فن‌های صنعتی 48V DC
- کنترل PWM بر اساس دما
- Redundancy: حداقل 2 فن

**Derating:**
- اگر دما >75°C → کاهش جریان به 80%
- اگر دما >85°C → قطع شارژ

---

## 10. HMI و نمایشگر

### 10.1 نمایشگر

**نمایشگر 7" تاچ TFT:**
- رزولوشن: 800x480
- Interface: MIPI DSI or RGB parallel
- تاچ: Capacitive (I2C)
- Brightness: قابل تنظیم (PWM)

**نمایش اطلاعات:**
```
┌────────────────────────────────┐
│  DC Fast Charging              │
│  ────────────────────────────  │
│  Voltage:    450 V             │
│  Current:    125 A             │
│  Power:      56.2 kW           │
│  ────────────────────────────  │
│  Energy:     12.5 kWh          │
│  Time:       00:14:32          │
│  Cost:       45,000 Rial       │
│  ────────────────────────────  │
│  SOC:        ██████░░░░  62%   │
│                                │
│  [ Stop Charging ]             │
└────────────────────────────────┘
```

### 10.2 LED های وضعیت

**RGB LED strip:**
- سبز: Idle/Available
- آبی پالس: Communicating (ISO 15118)
- آبی ثابت: Charging
- قرمز: Fault
- زرد: Maintenance mode

### 10.3 RFID Reader

**همان مدل AC:**
- MFRC522 (13.56 MHz)
- کارت MIFARE
- SPI به Main SoM

---

## 11. Communication Interfaces

### 11.1 Ethernet (اصلی)

**ماژول: داخلی در SoM**
- Gigabit Ethernet PHY
- کانکتور: RJ45 با LED
- پروتکل: OCPP 2.0.1 over WebSocket (WSS)
- Fallback: OCPP 1.6J

### 11.2 4G/LTE (بک‌آپ)

**ماژول: Quectel EC25-E یا SIM7600E**
- Interface: USB 2.0 به SoM
- سیم‌کارت: 4FF (Nano)
- Antenna: dual (main + diversity)
- Protocols: HTTPS, MQTT, WebSocket

### 11.3 WiFi (اختیاری)

**برای نصب داخلی یا configuration:**
- Module: ESP32-WROOM or built-in SoM WiFi
- فقط برای setup و monitoring محلی

### 11.4 CAN Bus (داخلی)

**3 کانال CAN:**
1. **CAN1**: Safety MCU ↔ Main SoM (high priority)
2. **CAN2**: Safety MCU ↔ Power Modules (control)
3. **CAN3**: اختیاری - توسعه/debug

**Transceivers:**
- TJA1050 (CAN 2.0B)
- یا TJA1043 (CAN-FD capable)

---

## 12. Power Supply Architecture

### 12.1 ورودی AC و PFC

**ورودی:**
- 3-phase 400V AC ±10%
- فرکانس: 50 Hz
- جریان: ~150A برای 120kW

**PFC Module:**
- ماژول آماده: Vienna Rectifier
- خروجی: 800V DC bus
- PFC: >0.95

### 12.2 تغذیه کنترل

**DC Rails مورد نیاز:**
```
800V DC Bus ──→ [Isolated DC-DC] ──→ +48V @ 10A (فن‌ها، کمکی)
                                  ├→ +24V @ 5A (کنتاکتورها)
                                  ├→ +12V @ 3A (PLC، آنالوگ)
                                  ├→ +5V @ 3A (دیجیتال، USB)
                                  └→ +3.3V @ 2A (MCU، logic)
```

**ماژول‌های منبع:**
- **Primary**: RECOM RPA60-HW (isolated 800V→48V)
- **Secondary**:
  - 48V→24V: TRACO TSR-3 series (buck)
  - 24V→12V: LM2576 (buck)
  - 12V→5V: LM2596
  - 5V→3.3V: AMS1117-3.3

**Backup Power:**
- باتری 12V / 7Ah (SLA) برای emergency shutdown
- در صورت قطع AC، کنتاکتورها بسته شوند و discharge

---

## 13. Safety & Protection

### 13.1 مدارات حفاظتی ورودی

**SPD (Surge Protection):**
- کلاس II - Type 2
- MOV varistors + GDT
- Protection level: 2.5kV

**MCB/MCCB:**
- 3-phase 160A Type C
- مشخصات قطع: 10kA

**Main Contactor AC:**
- برای قطع کامل ورودی
- کنترل از Emergency Stop

### 13.2 Emergency Stop

**دکمه E-Stop:**
- Mushroom push-button (40mm قرمز)
- NC contact (normally closed)
- Hard-wired به کنتاکتورها
- Interrupt به Safety MCU

**Sequence:**
```
E-Stop Pressed ──→ 1. قطع فوری کنتاکتورها (سخت‌افزاری)
                   2. Interrupt به Safety MCU
                   3. رمپ-down ماژول‌های قدرت
                   4. Discharge خازن‌ها
                   5. Log event
                   6. نمایش خطا روی HMI
```

### 13.3 Interlocks

**CP/PP Interlock (مطابق CCS2):**
- CP signal باید present باشد
- PP resistance صحیح (کابل متصل)

**Door Interlock (برای enclosure):**
- Micro-switch روی درب
- اگر باز شد → قطع فوری

**PLC Interlock:**
- ارتباط PLC باید برقرار باشد
- اگر قطع شد >5s → stop charging

---

## 14. Grounding & EMC

### 14.1 Grounding Architecture

**Multi-point Grounding:**
```
Chassis Ground (PE) ──┬── Power Stage Ground (PGND)
                      │
                      ├── Safety Circuits Ground (SGND)
                      │
                      ├── Control Ground (AGND)
                      │
                      └── Signal Ground (digital - DGND)

Single-point star connection at main PE terminal
```

### 14.2 EMI/EMC

**EMI Filters:**
- ورودی AC: 3-phase EMI filter (SCHAFFNER FN3258)
- خروجی DC: Common-mode choke + Y-caps

**Shielding:**
- Enclosure فلزی (IP54)
- Shield کابل‌های سیگنال
- Ferrite beads روی خطوط داده

**PCB Design:**
- 6-layer PCB (power + ground planes)
- Guard rings برای آنالوگ
- Via stitching برای EMI

---

## 15. Connectors & Cables

### 15.1 کانکتور CCS2

**کانکتور اصلی:**
- استاندارد: IEC 62196-3 (Type 2 Combo)
- پین‌ها:
  - DC+, DC-
  - CP (Control Pilot)
  - PP (Proximity Pilot)
  - PE (Protective Earth)
  - PLC+ / PLC- (برای ISO 15118)

**کابل:**
- طول: 5 متر (استاندارد)
- سطح مقطع: 50mm² (برای 200A)
- خنک‌کننده: برای >100A (مایع گردش)

### 15.2 کانکتورهای برد

**روی Control Board:**
- **DC Input**: Anderson PowerPole 350A
- **AC Input**: Terminal block 25mm²
- **CAN**: Phoenix Contact COMBICON
- **Ethernet**: RJ45 industrial
- **Power Modules**: Molex Mega-Fit (high current)
- **Sensors**: JST-XH series
- **Programming**: Tag-Connect (SWD/JTAG)

---

## 16. Mechanical & Thermal

### 16.1 ابعاد فیزیکی

**Control Board اصلی:**
- ابعاد: 300mm x 200mm
- لایه‌ها: 6-layer PCB
- ضخامت: 2mm
- مس: 3oz (105μm) برای قدرت، 1oz برای سیگنال

**Enclosure:**
- ابعاد کلی: 800mm (H) x 500mm (W) x 300mm (D)
- Protection: IP54 (outdoor)
- مواد: فولاد ضد زنگ یا آلومینیوم پودر-کوت

### 16.2 Cooling

**فن‌ها:**
- 3x 120mm فن صنعتی 48V DC
- Airflow: 200 CFM per fan
- کنترل PWM

**هیت‌سینک‌ها:**
- برای ماژول‌های قدرت
- برای Safety MCU و SoM (اگر performance بالا)

**سنسور:**
- Thermal switches (N.O. @ 85°C)

---

## 17. Firmware Architecture

### 17.1 Safety MCU Firmware

**RTOS: FreeRTOS (برای determinism)**

**Tasks:**
```c
void task_PreCharge(void);         // Pre-charge sequence
void task_ContactorControl(void);  // Contactor management
void task_IMD_Monitor(void);       // Insulation monitoring
void task_CurrentControl(void);    // Current regulation
void task_SafetyCheck(void);       // OV/UV/OC/OT checks
void task_CANComm(void);           // CAN communication
void task_Emergency(void);         // E-stop handler
```

**State Machine:**
```c
enum DCChargerState {
    STATE_INIT,           // Initialization & self-test
    STATE_STANDBY,        // Ready, waiting for vehicle
    STATE_HANDSHAKE,      // ISO 15118 / DIN handshake
    STATE_PRECHARGE,      // Pre-charge sequence
    STATE_CHARGING,       // Main charging
    STATE_RAMPDOWN,       // Current ramp-down
    STATE_DISCONNECT,     // Safe disconnect
    STATE_FAULT,          // Error state
    STATE_EMERGENCY       // E-stop
};
```

### 17.2 Main SoM Software

**OS: Embedded Linux (Yocto)**

**Stack:**
- **Application Layer**: Qt/QML (HMI)
- **Middleware**:
  - OCPP client (WebSocket)
  - ISO 15118 stack (via PLC module)
  - Payment gateway
- **System**: systemd, D-Bus
- **Drivers**: CAN, Ethernet, USB

**Services:**
```
├── ocpp-client.service      (OCPP WebSocket)
├── iso15118-stack.service   (PLC communication)
├── hmi-app.service          (QML GUI)
├── rfid-reader.service      (MFRC522)
├── payment-gateway.service  (wallet/USSD)
└── logger.service           (transaction logs)
```

---

## 18. Testing & Validation

### 18.1 Factory Tests

1. **Power-on Test:**
   - تمام voltages صحیح
   - MCU boot
   - Communication links

2. **Functional Test:**
   - CP/PP signals
   - IMD verification
   - Contactor operation
   - Pre-charge sequence
   - Sensor readings

3. **Safety Test:**
   - HiPot: 2kV AC 1 min
   - RISO: >10MΩ @ 500V
   - Earth continuity: <0.1Ω
   - E-stop response time

4. **Communication Test:**
   - CAN bus
   - Ethernet link
   - PLC modem
   - 4G connection

### 18.2 Field Tests

1. **Compatibility:**
   - تست با خودروهای مختلف (Tesla, BMW, Nissan, BYD, etc.)
   - ISO 15118 handshake
   - DIN 70121 fallback

2. **Performance:**
   - Full power test (60-120kW)
   - Efficiency measurement
   - Thermal performance
   - Long duration (8 hours continuous)

3. **EMC:**
   - Conducted emissions (EN 61000-6-3)
   - Radiated emissions
   - Immunity tests

---

## 19. Bill of Materials (BoM) - High Level

| جزء | شرح | قیمت تخمینی (USD) |
|-----|-----|-------------------|
| **Safety MCU** | STM32F765VIT6 + peripherals | $25 |
| **Main SoM** | i.MX8M Mini SoM + carrier | $180 |
| **PLC Module** | QCA7005 Green PHY + coupling | $60 |
| **ISO 15118 Stack** | Software license (یکبار) | $500-2000 |
| **IMD Module** | Bender isoPV800 | $400 |
| **DC Leakage RCM** | Bender RCMB-DC | $350 |
| **Current Sensors** | LEM HTFS 400-P x2 | $250 |
| **Voltage Sensors** | LEM DV series x2 | $120 |
| **DC Contactors** | TE EVC500 x3 | $450 |
| **Pre-charge Resistor** | 100Ω 100W | $15 |
| **Power Supply** | Isolated DC-DC converters | $150 |
| **Display** | 7" TFT Touch | $80 |
| **RFID Reader** | MFRC522 | $5 |
| **4G Module** | SIM7600E | $35 |
| **Enclosure** | IP54 metal cabinet | $300 |
| **PCB** | 6-layer, 300x200mm (qty 10) | $80 |
| **Passives & Misc** | Resistors, caps, connectors | $100 |
| **CCS2 Connector** | Phoenix Contact + cable (5m) | $600 |
| **Power Modules** | 3x 20kW DC/DC (main cost!) | $3000-6000 |
| **Assembly & Test** | - | $500 |
| **جمع (60kW)** | | **~$6,200-9,200** |

*توجه: هزینه اصلی در ماژول‌های قدرت است. Control board حدود $2,500.*

---

## 20. Compliance & Certification

### 20.1 استانداردهای الزامی

- ✅ **IEC 61851-23**: DC EV charging system requirements
- ✅ **IEC 61851-24**: Digital communication (control pilot)
- ✅ **ISO 15118**: V2G communication (PLC)
- ✅ **DIN 70121**: Legacy DC charging communication
- ✅ **IEC 60364-7-722**: Electrical installation
- ✅ **IEC 61000**: EMC requirements
- ✅ **IEC 60950/62368**: Safety

### 20.2 گواهینامه‌ها

- 🎯 **CE Marking** (اروپا)
- 🎯 **CharIN Certification** (CCS compatibility)
- 🎯 **ISIRI** (استاندارد ایران)
- 🎯 **Grid Connection Permit**

---

## 21. Development Roadmap

### Phase 1: Prototype (3-4 ماه)
- ✅ طراحی schematic و PCB
- ✅ انتخاب ماژول‌های قدرت
- ✅ ساخت نمونه اولیه control board
- ✅ Firmware پایه Safety MCU
- ✅ تست basic functionality

### Phase 2: Integration (2-3 ماه)
- ✅ یکپارچه‌سازی PLC module
- ✅ پیاده‌سازی ISO 15118 stack
- ✅ توسعه HMI روی SoM
- ✅ تست با خودروی واقعی
- ✅ Debugging و بهینه‌سازی

### Phase 3: Certification (4-6 ماه)
- ✅ تست EMC کامل
- ✅ Safety testing
- ✅ CharIN compatibility testing
- ✅ دریافت certifications

### Phase 4: Production (ongoing)
- ✅ DFM (Design for Manufacturing)
- ✅ تولید انبوه
- ✅ QC procedures
- ✅ After-sales support

---

## 22. تفاوت‌های کلیدی با AC Wallbox

| ویژگی | AC Wallbox | DC Fast Charger |
|-------|-----------|----------------|
| **MCU** | تک MCU (STM32G4) | Dual (Safety MCU + SoM) |
| **قدرت** | 7.4-22 kW | 60-120 kW |
| **ولتاژ خروجی** | AC 230/400V | DC 200-950V |
| **ارتباط خودرو** | CP/PP PWM (ساده) | ISO 15118 PLC (پیچیده) |
| **Insulation** | RCM Type B | IMD + DC Leakage |
| **Pre-charge** | ندارد | دارد (ضروری) |
| **Contactors** | AC (ارزان) | DC (گران، high-voltage) |
| **ماژول قدرت** | ندارد | 3-4 ماژول stackable |
| **HMI** | LED/OLED کوچک | 7" TFT تاچ |
| **Backend** | OCPP 1.6J (اختیاری) | OCPP 2.0.1 (ضروری) |
| **هزینه** | ~$200-300 | ~$6,000-9,000 |

---

## نتیجه‌گیری

این طراحی برد DC Fast Charger یک معماری **modular** و **scalable** است که:
1. ✅ از استانداردهای IEC 61851-23/24 و ISO 15118 پیروی می‌کند
2. ✅ Safety را در اولویت قرار داده (Dual MCU، IMD، Pre-charge)
3. ✅ از ماژول‌های آماده تجاری برای کاهش زمان توسعه استفاده می‌کند
4. ✅ قابلیت ارتقا از 60kW به 120kW را دارد
5. ✅ برای بازار ایران بهینه‌سازی شده (4G، USSD، قیمت)

**نسخه**: 1.0
**تاریخ**: 2026-01-01
**مؤلف**: Claude AI
**مبتنی بر**: DESIGN.md - Iran EV Charger Project
