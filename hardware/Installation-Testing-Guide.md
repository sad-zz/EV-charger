# راهنمای نصب، تست و راه‌اندازی
# EV Charger - AC Wallbox & DC Fast Charger

## فهرست مطالب

1. [ایمنی و احتیاط‌ها](#safety)
2. [ابزارهای مورد نیاز](#tools)
3. [مونتاژ PCB](#pcb-assembly)
4. [تست و کالیبراسیون](#testing)
5. [نصب مکانیکی](#mechanical-installation)
6. [راه‌اندازی نرم‌افزار](#software-setup)
7. [تست میدانی](#field-testing)
8. [عیب‌یابی](#troubleshooting)

---

<a name="safety"></a>
## 1. ایمنی و احتیاط‌ها

### ⚠️ هشدارهای ایمنی بحرانی

#### برای AC Wallbox:
- ✋ **ولتاژ کشنده**: 220V/400V AC می‌تواند کشنده باشد
- 🔌 **قطع برق**: قبل از هر کاری برق اصلی را قطع کنید
- 🧪 **تست ولتاژ**: با مولتی‌متر عدم وجود ولتاژ را تأیید کنید
- 🔒 **قفل برق**: از قفل Lockout/Tagout استفاده کنید
- 🧤 **دستکش عایق**: دستکش‌های عایق 1000V استفاده کنید

#### برای DC Fast Charger:
- ⚡ **ولتاژ بالا**: تا 1000V DC - خطر بسیار بالا!
- ⏱️ **خازن‌های شارژ**: حتی بعد از قطع برق، خازن‌ها شارژ هستند
- ⏳ **زمان discharge**: حداقل 5 دقیقه صبر کنید
- 📏 **فاصله ایمن**: از فاصله مناسب با قطعات HV رعایت کنید
- 👥 **کار تیمی**: هیچ‌گاه تنها روی DC charger کار نکنید

### EPP (الزامات تجهیزات حفاظت فردی)

```
✓ دستکش عایق 1000V
✓ عینک ایمنی
✓ کفش ایمنی عایق
✓ لباس کار سوخت‌گیر
✓ تشک عایق (برای کار روی DC)
```

### قوانین طلایی

1. **همیشه فرض کنید مدار تحت ولتاژ است**
2. **با یک دست کار کنید** (دست دیگر در جیب)
3. **تجهیزات اندازه‌گیری کالیبره شده** استفاده کنید
4. **مسیر فرار** را از قبل مشخص کنید
5. **در صورت شک، متوقف شوید**

---

<a name="tools"></a>
## 2. ابزارهای مورد نیاز

### ابزارهای اساسی

#### برای مونتاژ:
- [ ] هویه SMD (دمای قابل تنظیم 300-400°C)
- [ ] Hot air rework station (برای QFP/QFN)
- [ ] Flux و سیم لحیم 0.5mm
- [ ] Tweezers (پنس‌های دقیق)
- [ ] لوپ/میکروسکوپ
- [ ] مولتی‌متر دیجیتال
- [ ] منبع تغذیه آزمایشگاهی (0-30V, 0-5A)

#### برای تست:
- [ ] اسیلوسکوپ (حداقل 100MHz, 2 کانال)
- [ ] مولتی‌متر True-RMS
- [ ] کلمپ‌متر AC/DC
- [ ] مگر (Megohmmeter) 500V/1000V
- [ ] منبع CP/PP simulator (برای تست)
- [ ] Logic analyzer (برای debug CAN/SPI)

#### برای نصب:
- [ ] دریل و پیچ‌گوشتی
- [ ] سوکت و آچار (10-32mm)
- [ ] کابل‌کش و لوله‌برها
- [ ] ترمینال کریمپ
- [ ] برچسب‌زن و علائم ایمنی

### تجهیزات اختیاری (مفید):

- Thermal camera (برای hotspot detection)
- EMI scanner (برای EMC debugging)
- Load bank (شبیه‌سازی بار)
- Vehicle simulator (برای تست بدون خودرو)

---

<a name="pcb-assembly"></a>
## 3. مونتاژ PCB

### 3.1 بازرسی اولیه PCB

```
✓ PCB آسیب فیزیکی ندارد
✓ Track ها short ندارند (با مولتی‌متر)
✓ Silkscreen خوانا است
✓ سوراخ‌ها (vias/holes) باز هستند
✓ Surface تمیز و بدون آلودگی
```

### 3.2 ترتیب مونتاژ (توصیه شده)

#### مرحله 1: قطعات SMD کوچک
1. مقاومت‌ها و خازن‌های 0805/0603
2. دیودها و ترانزیستورهای SOT-23
3. LED های SMD

**نکته**: از stencil برای solder paste استفاده کنید

#### مرحله 2: IC های SMD
1. Opamps و قطعات SOIC
2. Regulators (TO-252)
3. MCU (LQFP) - **دقت ویژه به جهت pin 1**
4. Modules (W5500، ESP32، etc.)

**روش:**
```
1. Solder paste روی پدها
2. قرار دادن IC با pinset
3. Reflow با hot air (به آرامی از 200→320°C)
4. بازرسی زیر لوپ
5. رفع bridge با flux و فتیله
```

#### مرحله 3: قطعات Through-hole
1. کانکتورها و terminal blocks
2. خازن‌های الکترولیتیک
3. Fuse holders
4. Contactors/Relays

#### مرحله 4: Heatsinks و مکانیکی
1. نصب heatsinks روی regulators
2. Thermal pad/paste
3. پیچ و spacerها

### 3.3 بازرسی بعد از مونتاژ

**چک‌لیست:**
```
□ تمام قطعات نصب شده‌اند
□ Polarity درست است (LED, دیود، الکو)
□ Bridge/short وجود ندارد (بازرسی لوپ)
□ Solder joints براق و کامل هستند
□ قطعات صاف نشسته‌اند (نه کج)
```

**تست‌های اولیه:**
```
1. Continuity: GND plane به terminal GND
2. Resistance: +V rails به GND (باید >10kΩ)
3. Diode test: Polarity protection diodes
```

---

<a name="testing"></a>
## 4. تست و کالیبراسیون

### 4.1 Test Plan - AC Wallbox

#### Test 1: Power Supply Check

**Setup:**
```
AC Input: 220V از منبع ایزوله (isolation transformer)
Load: هیچ (اولین power-on)
```

**روش:**
```
1. مولتی‌متر را روی DC voltage قرار دهید
2. Probe را به خروجی +12V وصل کنید
3. برق را آرام وصل کنید
4. اندازه‌گیری:
   - +12V: 11.8-12.3V ✓
   - +5V: 4.9-5.1V ✓
   - +3.3V: 3.25-3.35V ✓
   - -12V: -11.8 to -12.3V ✓
5. Ripple check با oscope: <100mV pp
```

**معیار موفقیت**: تمام rails در محدوده ✓

#### Test 2: MCU Boot Check

**Setup:**
```
Power: از منبع آزمایشگاهی 12V @ 1A
UART: USB-Serial adapter به debug port
```

**روش:**
```
1. Flash کردن firmware تست (blink LED)
2. اتصال UART (115200 baud, 8N1)
3. Power on
4. مشاهده پیام boot در terminal
5. LED باید هر 500ms چشمک بزند
```

**خروجی نمونه:**
```
[OK] STM32G4 Boot v1.0
[OK] Clock: 170MHz
[OK] RAM Test: PASS
[OK] Flash CRC: OK
```

#### Test 3: CP Signal Generation

**Setup:**
```
Firmware: CP test mode (PWM 10%, 1kHz)
Oscope: Probe به CP output
```

**روش:**
```
1. فعال‌سازی CP PWM در firmware
2. مشاهده سیگنال روی oscope
3. اندازه‌گیری:
   - Frequency: 1000Hz ±1% ✓
   - Vhigh: +11.5 to +12.5V ✓
   - Vlow: -11.5 to -12.5V ✓
   - Duty: 10% ±1% ✓
   - Rise/Fall time: <100μs ✓
```

**نمونه تصویر oscope:**
```
      +12V ┌─┐ ┌─┐ ┌─┐
           │ │ │ │ │ │
        0V ┤ │ │ │ │ │
           │ └─┘ └─┘ └
     -12V  └─────────────
          ├─┤
          100μs (10%)
          ├────────────┤
             1ms (1kHz)
```

**تست Duty Cycle متغیر:**
```
10% → 6A   : Vhigh = +12V ✓
30% → 18A  : Vhigh = +12V ✓
50% → 30A  : Vhigh = +12V ✓
90% → 54A  : Vhigh = +12V ✓
```

#### Test 4: CP Sense (State Detection)

**Setup:**
```
CP Generator: Function generator (یا برد دیگر)
CP Input: از Test Point CP_SENSE
ADC: خواندن از firmware
```

**روش:**
```
شبیه‌سازی هر State با ولتاژهای مختلف:

State A (+12V):
  → ADC باید 12.0V بخواند

State B (+9V):
  → تقسیم ولتاژ با 2.7kΩ به CP
  → ADC باید 9.0V بخواند

State C (+6V):
  → تقسیم ولتاژ با 880Ω
  → ADC باید 6.0V بخواند

State D (+3V):
  → تقسیم ولتاژ با 240Ω
  → ADC باید 3.0V بخواند
```

**کالیبراسیون ADC:**
```c
// در firmware
#define CP_CALIB_OFFSET  0.05  // برای جبران خطای ADC
#define CP_CALIB_GAIN    1.02

float cp_voltage = (adc_raw * 3.3 / 4096.0) *
                   DIVIDER_RATIO * CP_CALIB_GAIN +
                   CP_CALIB_OFFSET;
```

#### Test 5: PP Resistance Reading

**Setup:**
```
Test Resistors: 1.5kΩ, 680Ω, 220Ω, 100Ω (1%)
PP Input: اتصال resistor بین PP و GND
```

**روش:**
```
R_test = 1.5kΩ:
  V_measure = 5V * 1.5k / (1k + 1.5k) = 3.0V
  → Firmware باید تشخیص دهد: 13A کابل ✓

R_test = 680Ω:
  V_measure = 5V * 680 / (1k + 680) = 2.02V
  → تشخیص: 20A کابل ✓

R_test = 220Ω:
  V_measure = 5V * 220 / (1k + 220) = 0.9V
  → تشخیص: 32A کابل ✓

R_test = 100Ω:
  V_measure = 5V * 100 / (1k + 100) = 0.45V
  → تشخیص: 63A کابل ✓
```

#### Test 6: Current Sensing

**Setup:**
```
CT Input: از Power Analyzer/Load simulator
Load: 10A, 20A, 30A AC
```

**روش:**
```
1. اعمال جریان 10A AC
2. خواندن ADC از CT output
3. محاسبه RMS در firmware
4. مقایسه با مرجع

مثال با SCT-013-000 (0-100A → 0-50mA):
  I_primary = 30A
  → I_secondary = 30A * (1/2000) = 15mA
  → V_burden = 15mA * 100Ω = 1.5V
  → V_adc (با bias 1.65V) = 1.65 ± 1.5V peak
  → RMS = 1.5/√2 = 1.06V RMS
  → Firmware محاسبه: I = 30A ✓
```

**کالیبراسیون:**
```c
// Calibration constants
#define CT_RATIO  2000.0   // 100A:50mA
#define BURDEN_R  100.0
#define ADC_SAMPLES 200    // 10 cycles @ 10kHz

float current_rms = calculate_rms() * CT_CALIB_GAIN;
```

**دقت مورد انتظار**: ±2% @ 10-100A

#### Test 7: Voltage Sensing

**Setup:**
```
AC Source: Variable AC (0-250V)
Reference: Calibrated multimeter
```

**روش:**
```
V_ac = 220V:
  → Divider: 220V * (4.7k / (470k + 4.7k)) = 2.18V
  → ADC (با نمونه‌برداری peak): ~3.1V
  → RMS calc: 220V ✓

تست در 200V, 220V, 240V
دقت: ±1%
```

#### Test 8: RCM Functionality

**Setup:**
```
RCM Module: نصب‌شده در مدار
Fault Simulator: Resistor 6kΩ از L به PE (ایجاد 36mA leak)
```

**روش:**
```
1. RCM self-test → LED سبز روشن
2. شبیه‌سازی نشتی 20mA AC → Warning
3. شبیه‌سازی نشتی 35mA AC → Trip!
4. زمان trip: <100ms ✓
5. پالس fault به MCU → Interrupt تأیید
```

#### Test 9: Temperature Monitoring

**Setup:**
```
Sensors: PT1000 / NTC
Reference: Thermocouple calibrated
```

**روش:**
```
1. دمای محیط (25°C):
   → PT1000: 1098Ω → ADC → 25°C ✓
   → NTC 10k: ~10kΩ → محاسبه Beta → 25°C ✓

2. حرارت دادن تا 50°C:
   → تأیید دقت ±1°C

3. آزمون آلارم:
   → Temp > 70°C → Warning ✓
   → Temp > 85°C → Shutdown ✓
```

#### Test 10: Contactor Control

**Setup:**
```
Contactor: نصب با بار dummy
Feedback: Auxiliary contact به MCU
```

**روش:**
```
1. MCU command: CLOSE contactor
   → GPIO HIGH → Optocoupler → MOSFET → Coil energized
   → Aux contact بسته می‌شود
   → Feedback به MCU: "Contactor Closed" ✓
   → زمان: <50ms

2. MCU command: OPEN
   → GPIO LOW → Coil de-energized
   → Contactor باز می‌شود
   → Feedback: "Contactor Open" ✓

3. Fault test:
   → Command CLOSE اما feedback همچنان OPEN
   → MCU باید خطا دهد ✓
```

#### Test 11: OCPP Communication

**Setup:**
```
Network: Ethernet/WiFi به router
Backend: OCPP test server (مثلاً SteVe)
```

**روش:**
```
1. Configure endpoint: ws://server:8080/steve/websocket/CentralSystemService
2. Reboot charger
3. مشاهده در SteVe:
   → BootNotification received ✓
   → Heartbeat هر 60s ✓
4. شروع تراکنش از server:
   → RemoteStartTransaction → charger اجرا می‌کند ✓
5. Log:
   → MeterValues ارسال می‌شود ✓
```

#### Test 12: RFID Authentication

**Setup:**
```
RFID Module: MFRC522
Card: MIFARE Classic
```

**روش:**
```
1. نزدیک کردن کارت
2. UID خوانده می‌شود
3. ارسال به backend (اگر OCPP)
4. تأیید/رد → LED indicator ✓
```

---

### 4.2 Test Plan - DC Fast Charger

#### Test 1-2: Power Supply (مشابه AC)

همان تست‌های AC با ولتاژهای بیشتر:
- +48V, +24V, +12V, +5V, +3.3V

#### Test 3: IMD (Insulation Monitoring)

**Setup:**
```
IMD Module: Bender isoPV800 نصب شده
DC Bus: Simulate با منبع 400V (با احتیاط!)
```

**روش:**
```
1. IMD self-test → OK LED
2. DC bus = 400V، عایق سالم:
   → IMD نشان می‌دهد >100kΩ/V = >40MΩ ✓

3. شبیه‌سازی خطا:
   → اتصال 50kΩ از DC+ به PE
   → IMD alarm ✓
   → Fault signal به Safety MCU ✓

4. IMD فوری کنتاکتورها را قطع می‌کند ✓
```

**⚠️ احتیاط**: این تست فقط توسط فرد مجرب و با EPP کامل

#### Test 4: DC Voltage/Current Sensing

**Setup:**
```
منبع DC: 200-400V (variable)
بار: Load bank 0-50A
سنسورها: LEM DV + HTFS
```

**روش:**
```
V_dc = 300V:
  → LEM DV output: 1.5V (ratio 200:1)
  → ADC: 300V ✓
  → دقت: ±0.5%

I_dc = 30A:
  → LEM HTFS output: 0.3V (ratio 100:1)
  → ADC: 30A ✓
  → دقت: ±0.5%
```

#### Test 5: Pre-charge Sequence

**Setup:**
```
DC Bus: 400V
Capacitor: 1000μF @ output (شبیه‌سازی باتری)
Oscope: CH1=DC+, CH2=Output+
```

**روش:**
```
1. همه کنتاکتورها باز
2. Command pre-charge:

   t=0ms: K2 بسته می‌شود (DC-)
   t=10ms: K3 بسته (pre-charge با 100Ω)

   → ولتاژ output آرام بالا می‌رود:
     V(t) = Vbus * (1 - e^(-t/RC))
     با R=100Ω، C=1000μF → τ=100ms

   t=500ms: V_output = 0.99 * Vbus = 396V

   → Safety MCU تأیید: V_out > 90% V_bus ✓

   t=510ms: K1 بسته (main DC+)
   t=520ms: K3 باز (pre-charge bypass)

   ✓ جریان inrush < 10A
   ✓ زمان کل: ~0.5s
```

**نمونه oscope:**
```
Vbus ┌──────────────────
     │
     │         ┌────────  V_output (exponential rise)
     │      ┌──┘
     │   ┌──┘
     │┌──┘
  0V └────────────────
     K2  K3      K1 K3
     ON  ON      ON OFF
```

#### Test 6: Power Module Control (CAN)

**Setup:**
```
Power Modules: 3x 20kW (CAN addresses 0x01, 02, 03)
CAN Bus: Safety MCU master
```

**روش:**
```
1. Broadcast enable:
   → تمام ماژول‌ها: Status OK ✓

2. Set voltage = 400V, Current = 100A:
   → ماژول 1: V=400V, I=33.3A ✓
   → ماژول 2: V=400V, I=33.3A ✓
   → ماژول 3: V=400V, I=33.3A ✓
   → Current sharing: ±2A tolerance ✓

3. Fault simulation (قطع یک ماژول):
   → ماژول 1: Fault → disabled
   → ماژول 2,3: I=50A each ✓
   → Total: 100A maintained (derating) ✓
```

#### Test 7: PLC Communication (ISO 15118)

**Setup:**
```
PLC Module: QCA7005
Vehicle: Simulator یا خودرو واقعی
Software: ISO 15118 stack
```

**روش:**
```
1. Cable plug → CP/PP تشخیص
2. PLC handshake:
   → SLAC (Signal Level Attenuation)
   → Network setup
   → IPv6 link established ✓

3. ISO 15118 messages:
   → SessionSetup ✓
   → ServiceDiscovery ✓
   → ChargeParameterDiscovery:
     • Vehicle: "I need 400V, 100A"
     • EVSE: "OK, I can provide" ✓
   → PowerDelivery ✓
   → Charging starts ✓
```

**DIN 70121 Fallback:**
```
اگر خودرو ISO 15118 support نکند:
  → Fallback to DIN 70121
  → ساده‌تر (CAN-like protocol)
  → همچنان کار می‌کند ✓
```

#### Test 8: End-to-End Charging Test

**Setup:**
```
Charger: DC 60kW
Load: Electronic load 0-150A, 200-500V
یا خودرو واقعی
```

**Scenario:**
```
1. Idle:
   → Display: "Available"
   → LED: سبز

2. کابل plug:
   → CP/PP detect ✓
   → PLC handshake ✓
   → Display: "Authenticating"

3. RFID swipe:
   → Auth OK ✓
   → OCPP → StartTransaction ✓

4. ISO 15118 parameter exchange:
   → Vehicle requests: 400V, 80A
   → EVSE accepts ✓

5. Pre-charge:
   → Sequence اجرا می‌شود ✓

6. Charging:
   → Power ramp: 0 → 32kW در 10s
   → Current = 80A ✓
   → Voltage = 400V ✓
   → Display: Real-time kW/kWh/time

7. Monitoring:
   → Temp sensors: <60°C ✓
   → IMD: >100kΩ/V ✓
   → No alarms ✓

8. Stop (از خودرو):
   → Power ramp down: 32kW → 0 در 5s
   → کنتاکتورها باز می‌شوند
   → Discharge resistors فعال
   → V_output → 0V در <60s ✓

9. Unplug:
   → Transaction end
   → OCPP → StopTransaction (با kWh) ✓
   → Display: "Session Complete"
```

**Performance Targets:**
```
✓ Time to start charging: <30s
✓ Efficiency: >94%
✓ Derating: <5% در 30 دقیقه
✓ بدون هیچ warning/fault
```

---

<a name="mechanical-installation"></a>
## 5. نصب مکانیکی

### 5.1 نصب AC Wallbox

#### محل نصب

**الزامات:**
```
✓ فاصله از زمین: 1.2-1.6m
✓ محافظت در برابر باران (IP54+ یا سایبان)
✓ دسترسی به برق 3-فاز (یا تک‌فاز)
✓ فاصله از محل پارک: <6m (طول کابل)
✓ دسترسی به اینترنت (LAN یا 4G)
```

#### نصب فیزیکی

**مراحل:**
```
1. انتخاب محل → علامت‌گذاری سوراخ‌ها
2. حفر (با مته 8mm برای پیچ M8)
3. نصب Fischer/دوبل در دیوار
4. نصب بک‌پلیت Wallbox با پیچ
5. کابل‌کشی AC از تابلو برق
6. اتصال Ethernet (اگر available)
```

#### اتصال برق

**⚠️ فقط توسط برق‌کار مجاز**

```
Diagram اتصال:

Main Panel ─── MCB 40A ──┬─── L (قهوه‌ای)
                         ├─── N (آبی)
                         └─── PE (زرد-سبز)
                                  │
                                  ↓
                         [AC Wallbox Terminals]

سه‌فاز:
  L1 (قهوه‌ای) ──→ Terminal L1
  L2 (مشکی)    ──→ Terminal L2
  L3 (خاکستری) ──→ Terminal L3
  N  (آبی)     ──→ Terminal N
  PE (زرد-سبز) ──→ Terminal PE (اتصال محکم!)
```

**تست بعد از اتصال:**
```
1. MCB OFF
2. چک اتصالات با مولتی‌متر (continuity)
3. Megger test: L-PE >10MΩ @ 500V ✓
4. MCB ON
5. اندازه‌گیری ولتاژ:
   تک‌فاز: L-N = 220V ±10%
   سه‌فاز: L1-L2 = 400V, L1-N = 230V
6. Earth continuity: PE-chassis < 0.1Ω
```

---

### 5.2 نصب DC Fast Charger

#### محل نصب

**الزامات (سخت‌تر از AC):**
```
✓ Concrete pad (صاف و سطح)
✓ Bollards محافظ (برای جلوگیری از تصادف)
✓ سایبان (برای حفاظت از عناصر)
✓ اتصال به زمین مناسب (<10Ω)
✓ فضای تهویه (>50cm از دیوار)
✓ دسترسی برق 3-phase 400V (>100A)
✓ Ethernet backbone (برای OCPP)
```

#### اتصال برق

**⚠️ فقط توسط برق‌کار صنعتی مجاز**

```
Diagram:

Main Distribution ─── MCCB 160A ──┬─── L1
                                   ├─── L2
                                   ├─── L3
                                   ├─── N
                                   └─── PE
                                         │
                                         ↓
              [Isolator/Disconnect Switch] (قابل قفل)
                                         │
                                         ↓
                      ┌───────────────────┴────────────┐
                      │  DC Fast Charger Input         │
                      │  Terminal Block (25mm²)        │
                      └─────────────────┬──────────────┘
                                        │
                                   [PFC Module]
                                        │
                                   [DC Bus 800V]
```

**سیم‌کشی:**
```
Cable size:
  برای 60kW @ 400V:
    I = 60000 / (√3 * 400 * 0.95) ≈ 91A
    → کابل 25mm² (حداقل)

  برای 120kW:
    I ≈ 182A
    → کابل 70mm²

  PE: همان سایز فاز (یا حداقل 25mm²)
```

**Grounding:**
```
1. اتصال PE از تابلو به chassis
2. Earth rod: <10Ω (ترجیحاً <5Ω)
3. Bond تمام قسمت‌های فلزی
4. تست زمین: Earth loop impedance test
```

---

<a name="software-setup"></a>
## 6. راه‌اندازی نرم‌افزار

### 6.1 AC Wallbox Firmware

#### ابزارهای مورد نیاز

```
- STM32CubeIDE (یا PlatformIO)
- ST-Link V2 programmer
- Git
```

#### Flashing Firmware

**مراحل:**
```bash
# 1. Clone repository
git clone https://github.com/your-repo/ac-wallbox-fw.git
cd ac-wallbox-fw

# 2. Build
make clean
make all

# 3. Flash
st-flash write build/firmware.bin 0x8000000

# یا با OpenOCD:
openocd -f interface/stlink-v2.cfg \
        -f target/stm32g4x.cfg \
        -c "program build/firmware.elf verify reset exit"
```

#### Configuration (اولین بوت)

**Serial Console (115200 baud):**
```
=== AC Wallbox Configuration ===

1. Device ID: ACWB-001234
2. Max Current: [32] A → 32
3. Network Mode:
   [1] Ethernet
   [2] WiFi
   [3] 4G
   → 1 (Ethernet)

4. OCPP Server: ws://backend.local/ocpp
5. RFID Mode: [Y/n] → Y

Saving config... OK
Rebooting...
```

**یا از Web UI:**
```
1. در اولین بوت، WiFi AP mode: "EV-Wallbox-Setup"
2. اتصال با موبایل/لپتاپ
3. باز کردن http://192.168.4.1
4. پر کردن فرم configuration
5. Save & Reboot
```

---

### 6.2 DC Fast Charger Software

#### Main SoM (i.MX8M / STM32MP1)

**OS: Embedded Linux (Yocto)**

**اولین boot از eMMC:**
```bash
# تنظیم شبکه
nmcli device wifi connect "YourWiFi" password "pass"

# یا Ethernet (DHCP)
systemctl restart NetworkManager

# چک IP
ip addr show

# SSH access
ssh root@<IP>
```

**Deploy OCPP Client:**
```bash
# Install dependencies
apt-get update
apt-get install python3-websocket libssl-dev

# Clone OCPP client
git clone https://github.com/mobilityhouse/ocpp.git
cd ocpp

# Configure
nano config.yaml
---
charger_id: "DC-FAST-001"
ocpp_server: "wss://backend.example.com/ocpp"
ocpp_version: "2.0.1"
---

# Run as service
systemctl enable ocpp-client
systemctl start ocpp-client
systemctl status ocpp-client
```

#### Safety MCU (STM32F7)

**Firmware upload:**
```bash
# با ST-Link
st-flash write safety-mcu.bin 0x8000000

# Verification
st-info --probe
```

**Initial Calibration:**
```
Safety MCU Console:

> calib voltage 400.0
Calibrating voltage sensor to 400.0V reference...
ADC raw: 3278
Calculated: 398.5V
Offset: +1.5V
Gain: 1.0037
[OK] Calibration saved

> calib current 100.0
Calibrating current sensor to 100.0A reference...
[OK] Calibration saved
```

---

<a name="field-testing"></a>
## 7. تست میدانی

### 7.1 AC Wallbox - Field Test

**Test با خودرو واقعی:**

```
Vehicle: Nissan Leaf / Tesla Model 3 / etc.

1. Plug cable → Wallbox تشخیص می‌دهد
2. RFID card → Authentication OK
3. CP PWM starts (duty cycle بر اساس vehicle)
4. Contactor بسته می‌شود
5. Charging شروع می‌شود
   → Vehicle dashboard: "Charging..."

6. مانیتور:
   - جریان AC (با کلمپ‌متر): ~16A ✓
   - ولتاژ: 230V stable ✓
   - دمای کانکتور: <50°C ✓
   - RCM: No fault ✓

7. شارژ 30 دقیقه continuous:
   → هیچ alarm یا shutdown
   → دمای wallbox: <60°C ✓

8. Stop از خودرو → شارژ قطع ✓
9. Unplug → OCPP StopTransaction sent ✓
```

---

### 7.2 DC Fast Charger - Field Test

**Test با خودرو CCS2:**

```
Vehicle: BMW i3 / VW ID.4 / Hyundai Ioniq 5

1. Plug CCS2 cable
   → CP/PP تشخیص ✓
   → PLC handshake شروع

2. Display: "Communicating..."
   → ISO 15118 messages exchange
   → Vehicle: "Ready for 400V, 50kW"

3. RFID → Auth OK

4. Pre-charge:
   → 0 → 400V در 0.5s ✓
   → Current spike: <5A (خیلی خوب!) ✓

5. Charging:
   → Power ramp: 0 → 50kW در 10s
   → Stable @ 50kW
   → Vehicle SOC: 20% → 80% در ~30 دقیقه

6. Monitoring (هر 5 دقیقه):
   Min 0:  50kW, 125A, 400V, Temp 25°C
   Min 5:  50kW, 125A, 400V, Temp 42°C
   Min 10: 50kW, 125A, 400V, Temp 48°C
   Min 15: 48kW, 120A, 400V, Temp 52°C (slight derating OK)
   Min 20: 45kW, 112A, 401V, Temp 54°C
   Min 25: 38kW, 95A,  400V, Temp 53°C (vehicle tapering)
   Min 30: 25kW, 62A,  403V, Temp 50°C
   → Stop (80% reached)

7. Shutdown sequence:
   → Power ramp down: 25kW → 0 در 5s ✓
   → Contactors باز ✓
   → Display: "Complete: 24.5 kWh"

8. Unplug:
   → OCPP StopTransaction
   → Backend: Invoice generated ✓
```

**معیارهای موفقیت:**
```
✓ شروع شارژ: <30s از plug
✓ هیچ CCS communication error
✓ دمای پایدار <60°C
✓ بدون trip/fault
✓ Vehicle شارژ می‌شود
✓ انرژی صحیح logged
```

---

<a name="troubleshooting"></a>
## 8. عیب‌یابی

### مشکلات رایج AC Wallbox

#### Problem 1: خودرو شارژ نمی‌شود

**تشخیص:**
```
1. چک CP signal با oscope:
   - آیا PWM تولید می‌شود? → اگر نه: firmware issue
   - Voltage levels OK? → اگر نه: CP driver fault

2. چک PP:
   - آیا کابل تشخیص داده می‌شود؟

3. چک Contactor:
   - آیا بسته می‌شود؟
   - LED indicator روی contactor?
   - Measure coil voltage (باید 12V)

4. چک RCM:
   - آیا trip کرده؟
   - Reset و دوباره تست
```

**راه‌حل‌های معمول:**
```
→ CP signal weak: بررسی R1/R2 (1kΩ)
→ Contactor نمی‌بندد: چک driver MOSFET
→ RCM trip: نشتی زمین → چک کابل/کانکتور
```

#### Problem 2: جریان کم (مثلاً 6A به جای 16A)

**تشخیص:**
```
1. چک CP duty cycle:
   - 10% → 6A
   - 50% → 30A
   - آیا firmware correct duty می‌فرستد?

2. چک PP resistance:
   - کابل چه ظرفیتی دارد؟
   - اگر 13A کابل → max 6A درست است
```

**راه‌حل:**
```
→ کابل upgrade به 32A
→ یا در firmware max current را override کنید (با احتیاط!)
```

---

### مشکلات رایج DC Fast Charger

#### Problem 1: PLC Communication Failure

**تشخیص:**
```
1. چک PLC module LED:
   - Link LED روشن؟
   - Activity LED چشمک می‌زند؟

2. چک coupling circuit:
   - Cap 1μF OK?
   - Transformer اتصال دارد؟

3. Log ISO 15118:
   - کدام مرحله fail می‌شود؟
   - SLAC? → PLC layer issue
   - SessionSetup? → application layer
```

**راه‌حل:**
```
→ PLC module reboot
→ چک grounding CP line
→ Firmware update ISO 15118 stack
→ Fallback به DIN 70121
```

#### Problem 2: Pre-charge Timeout

**تشخیص:**
```
1. Measure V_output هنگام pre-charge:
   - آیا بالا می‌رود؟
   - اگر نه → short circuit در خروجی
   - اگر خیلی آهسته → R_precharge زیاد یا سوخته

2. چک K3 contactor:
   - آیا بسته می‌شود؟
```

**راه‌حل:**
```
→ بررسی R_precharge (باید 100Ω، not open)
→ چک K3 auxiliary contact
→ بررسی خروجی برای short
```

#### Problem 3: IMD Fault

**تشخیص:**
```
1. IMD display/LED چه می‌گوید؟
   - RISO value کمتر از threshold

2. Disconnect output → RISO بهبود یافت؟
   - بله → مشکل در خودرو/کابل
   - نه → مشکل در charger
```

**راه‌حل:**
```
→ چک کابل CCS2 برای آسیب
→ بررسی عایق کاری ماژول‌های قدرت
→ چک اتصالات DC bus
```

---

## پیوست: چک‌لیست نهایی قبل از تحویل

### AC Wallbox

```
□ تمام تست‌های electrical انجام شده
□ CP/PP صحیح کار می‌کند
□ RCM trip test OK
□ با حداقل 2 مدل خودرو تست شده
□ OCPP به backend متصل است
□ برچسب‌های ایمنی نصب شده
□ دفترچه راهنمای کاربر ارائه شده
□ گارانتی ثبت شده
```

### DC Fast Charger

```
□ تمام تست‌های HV انجام شده
□ IMD و leakage detection OK
□ Pre-charge تست شده
□ ISO 15118 handshake OK
□ Full power test 1 ساعت انجام شده
□ با حداقل 3 مدل خودرو تست شده
□ OCPP 2.0.1 متصل و functional
□ Emergency stop تست شده
□ تمام سنسورها کالیبره شده
□ Documentation کامل تحویل شده
□ Training به اپراتور داده شده
```

---

**نسخه**: 1.0
**تاریخ**: 2026-01-01
**مؤلف**: Claude AI
**پروژه**: Iran EV Charger

**⚠️ یادآوری نهایی**:
همیشه ایمنی را در اولویت قرار دهید. در صورت تردید، از متخصص کمک بگیرید.
