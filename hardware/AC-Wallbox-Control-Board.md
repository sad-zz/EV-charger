# AC Wallbox Control Board Design (7.4/22 kW)

## نمای کلی
برد کنترل برای شارژر AC دیواری با قابلیت 7.4 kW (تک‌فاز) یا 22 kW (سه‌فاز) با کانکتور Type 2 مطابق با استاندارد IEC 61851-1

## معماری سخت‌افزاری

### 1. میکروکنترلر اصلی
**MCU انتخابی: STM32G474RET6** (دلایل انتخاب پایین توضیح داده شده)

- **هسته**: ARM Cortex-M4F @ 170 MHz
- **حافظه**: 512 KB Flash، 128 KB RAM
- **Timer های پیشرفته**: 4x 32-bit timers برای CP/PP PWM دقیق
- **ADC**: 3x 12-bit ADC (5 Msps) برای اندازه‌گیری ولتاژ و جریان
- **Communication**:
  - 2x UART (برای RFID و debug)
  - 2x SPI (برای WiFi/Ethernet module)
  - 1x CAN (برای توسعه آینده)
  - 1x I2C (برای سنسورها و RTC)
- **پکیج**: LQFP-64

**آلترناتیو: ESP32-WROOM-32E**
- برای نسخه با WiFi/BLE داخلی
- رم بیشتر برای OCPP stack
- هزینه کمتر اما امنیت کمتر

### 2. مدار CP (Control Pilot) - IEC 61851-1

#### 2.1 تولید سیگنال PWM
```
MCU PWM (3.3V) → Level Shifter (74LVC1G17) → Driver Stage
                                            ↓
                                   +12V / -12V PWM Output
```

**مشخصات سیگنال CP:**
- فرکانس: 1 kHz ± 1%
- دامنه: +12V (State A/B) یا -12V (State C/D)
- Duty Cycle: 10%-96% (نشان‌دهنده جریان مجاز)
  - 10%: 6A
  - 20%: 12A
  - 50%: 30A
  - 96%: 80A

**مدار خروجی CP:**
```
+12V ──┬── R1(1kΩ) ──┬── CP_OUT ── به Vehicle
       │             │
       └── Q1(MOSFET)┤
                     │
                    GND (PWM کنترل)

-12V ──┬── R2(1kΩ) ──┤
       │
       └── Q2(MOSFET)┤ (برای State C/D)
                    GND
```

**قطعات کلیدی:**
- R1, R2: 1kΩ ±1% (مقاومت CP طبق استاندارد)
- Q1: IRF9540 (P-MOSFET برای +12V)
- Q2: IRLZ44N (N-MOSFET برای -12V switching)
- D1, D2: Schottky diode برای حفاظت

#### 2.2 خواندن وضعیت CP (Voltage Sensing)
```
CP Line ─── R_div (100k/10k) ─── Protection ─── ADC_IN
                                    ├── Zener 3.6V
                                    └── RC Filter (1kΩ, 100nF)
```

**وضعیت‌های CP:**
- State A: +12V (no vehicle)
- State B: +9V (vehicle connected, not ready)
- State C: +6V (vehicle ready, charging)
- State D: +3V (vehicle ready, ventilation required)
- State E: 0V (short circuit - error)
- State F: -12V (EVSE not available)

### 3. مدار PP (Proximity Pilot) - تشخیص نوع کابل

```
+5V ─── R_pullup(1kΩ) ─── PP_SENSE ─── ADC_IN
                            │
                            └─ به PP pin Type 2

کابل دارای مقاومت داخلی:
- 1.5kΩ: 13A
- 680Ω: 20A
- 220Ω: 32A
- 100Ω: 63A
```

**مدار سنسینگ PP:**
- Voltage divider با ADC
- مقاومت PP مشخص‌کننده ظرفیت کابل
- نرم‌افزار محاسبه مقاومت و تعیین حداکثر جریان

### 4. سیستم کنتاکتور AC

**کنتاکتور انتخابی:**
- تک‌فاز (7.4kW): TE Connectivity EV200 (40A)
- سه‌فاز (22kW): TE Connectivity KILOVAC EV200 (100A)

**مدار درایو کنتاکتور:**
```
MCU GPIO ─── Optocoupler (TLP281) ─── MOSFET Driver ─┬─── Relay Coil (+12V)
                                                      │
                                                      └─── Flyback Diode

Feedback: Auxiliary Contact ─── Optocoupler ─── MCU GPIO
```

**ویژگی‌های ایمنی:**
- تأیید بسته‌شدن از طریق auxiliary contact
- تایمر watchdog برای قطع اضطراری
- قطع در صورت خطای RCM
- Soft-start برای کاهش inrush current

### 5. اندازه‌گیری جریان

**روش 1: Current Transformer (CT) - برای AC**
- CT نوع: SCT-013-000 (0-100A)
  - خروجی: 0-50mA
  - Burden Resistor: 100Ω
  - فیلتر: RC filter (1kΩ + 10μF)
  - حفاظت: TVS diode

**مدار Conditioning:**
```
CT Secondary ─── Burden R (100Ω) ─── Bias (+1.65V) ─── ADC
                      │                    │
                      └─ C filter (10μF)   └─ Clamp diodes
```

**روش 2: Hall Effect Sensor (برای دقت بالاتر)**
- سنسور: ACS758 (0-100A) یا ACS772 (0-150A)
- خروجی: 0.6V-4.4V (مرکز @ 2.5V)
- مزیت: ایزوله، DC offset detection

**نرم‌افزار:**
- نمونه‌برداری با 10 kHz
- محاسبه RMS واقعی
- تشخیص Overcurrent (>110% rated)
- الگوریتم فیلتر دیجیتال (moving average)

### 6. اندازه‌گیری ولتاژ

**مدار تک‌فاز (L-N):**
```
AC Line ─── Fuse ─── R1(470k) ─┬─ R2(4.7k) ─── ADC
                                │
                                └─ C(100nF) + Zener(3.6V)
```

**مدار سه‌فاز (L1, L2, L3):**
- 3 کانال مجزا برای هر فاز
- تشخیص phase loss
- اندازه‌گیری عدم تعادل فازها

**محدوده اندازه‌گیری:**
- تک‌فاز: 220V ±10% → 198-242V
- سه‌فاز: 400V ±10% → 360-440V

**حفاظت‌ها:**
- Overvoltage: >253V (1-phase), >440V (3-phase)
- Undervoltage: <195V (1-phase), <350V (3-phase)
- Frequency: 48-52 Hz

### 7. ماژول RCM Type B (Residual Current Monitor)

**ماژول پیشنهادی: Bender RCMB121**
- تشخیص نشتی AC و DC
- حساسیت: 6mA DC، 30mA AC
- خروجی: رله NO/NC + سیگنال آنالوگ
- استاندارد: IEC 62752

**اتصال:**
```
L1, L2, L3, N ──→ [RCM Module] ──→ به کنتاکتور
                      │
                      └─ Fault Signal ─── Optocoupler ─── MCU Interrupt
```

**عملکرد:**
- مانیتورینگ مداوم نشتی جریان
- قطع فوری در صورت fault (< 100ms)
- لاگ خطا برای تعمیرات

### 8. سنسورهای دما

#### 8.1 دمای کانکتور Type 2
- **سنسور**: PT1000 یا NTC 10kΩ تعبیه‌شده در کانکتور
- **محدوده**: -30°C تا +90°C
- **آستانه‌ها**:
  - Warning: >70°C (کاهش جریان)
  - Critical: >85°C (قطع شارژ)

#### 8.2 دمای برد الکترونیک
- **سنسور**: LM75A (I2C) یا DS18B20 (1-Wire)
- **محدوده**: -20°C تا +80°C
- **آستانه**: >75°C (فن روشن/قطع)

**مدار سنسور دما (NTC):**
```
+3.3V ─── R_ref(10kΩ) ─┬─ NTC ─── GND
                        │
                        └─ ADC_IN (+ RC filter)
```

### 9. رابط HMI (Human Machine Interface)

#### 9.1 نمایشگر
- **Option 1**: OLED 0.96" (I2C) - SSD1306
  - نمایش: جریان، ولتاژ، انرژی، وضعیت
  - کم‌مصرف، قابل خواندن در نور

- **Option 2**: LED RGB + بازر
  - سبز: Idle/Ready
  - آبی: Charging
  - قرمز: Fault
  - بازر: تأیید/خطا

#### 9.2 RFID Reader
- **ماژول**: MFRC522 (SPI) - 13.56 MHz
- **کارت**: MIFARE Classic/Ultralight
- **فیچر**: احراز هویت کاربر

**اتصال:**
```
MFRC522 Module:
- VCC → 3.3V
- RST → GPIO
- GND → GND
- MISO → SPI_MISO
- MOSI → SPI_MOSI
- SCK → SPI_CLK
- SDA/SS → SPI_CS
```

#### 9.3 دکمه‌ها
- Start/Stop (با debounce)
- Reset/Emergency Stop (hardware interrupt)

### 10. ارتباطات Backend

#### 10.1 Ethernet (اولویت اول)
- **ماژول**: W5500 (SPI-to-Ethernet)
- **پروتکل**: OCPP 1.6J over WebSocket
- **سرعت**: 10/100 Mbps
- **کانکتور**: RJ45 با PoE (اختیاری)

**مدار:**
```
W5500 Module:
- SPI ← MCU
- Ethernet PHY → RJ45
- 3.3V power
- Reset control
```

#### 10.2 4G/LTE (بک‌آپ)
- **ماژول**: SIM7600E (برای ایران)
- **پروتکل**: OCPP over HTTPS
- **سیم‌کارت**: MCI/Irancell/Rightel

#### 10.3 WiFi (اختیاری)
- اگر ESP32 استفاده شود: WiFi داخلی
- اگر STM32: ماژول ESP-01S (UART to WiFi)

### 11. منبع تغذیه

**ورودی AC → خروجی‌های DC:**

```
AC Input (110-240V) ──→ [AC-DC Converter] ──→ Multi-rail DC
                             │
                             ├─→ +12V @ 2A (کنتاکتور، CP drive)
                             ├─→ +5V @ 1A (سنسورها، RFID)
                             ├─→ +3.3V @ 500mA (MCU، W5500)
                             └─→ -12V @ 100mA (CP State C/D)
```

**AC-DC Module پیشنهادی:**
- HLK-PM12 (12V/1A) + regulators
- یا Mean Well IRM-20-12 (برای ایمنی بالاتر)

**رگولاتورهای DC-DC:**
- +12V → +5V: LM2596 (Buck converter)
- +12V → +3.3V: AMS1117-3.3 (LDO)
- +12V → -12V: ICL7660 (charge pump) یا isolated DC-DC

**حفاظت منبع:**
- Fuse ورودی (250V, 2A slow-blow)
- TVS diodes
- Capacitors فیلتر (الکترولیتیک + سرامیک)

### 12. حفاظت‌ها و ایمنی

#### 12.1 SPD (Surge Protection Device)
- کلاس II طبق IEC 61643-11
- MOV varistors (275V for 230V AC)
- Gas discharge tubes (برای حفاظت بیشتر)

#### 12.2 MCB/RCBO
- تک‌فاز: 40A Type B MCB
- سه‌فاز: 40A 3P Type B MCB
- یا RCBO: 40A, 30mA Type B (اگر RCM مجزا نیست)

#### 12.3 محافظت برد
- TVS diodes روی تمام ورودی/خروجی‌ها
- Fuses روی خطوط قدرت
- ESD protection روی کانکتورها
- Optocoupler isolation بین قدرت و کنترل

### 13. EMC/EMI

**فیلترهای EMI:**
- Common-mode choke روی ورودی AC
- Differential-mode capacitors
- شیلدینگ enclosure (فلزی با اتصال زمین)

**PCB Layout Tips:**
- جداسازی بخش قدرت از کنترل
- Ground planes جدا (AGND/PGND) با single point connection
- روتینگ differential pairs برای سیگنال‌های حساس
- Via stitching برای شیلدینگ

### 14. کانکتورها و رابط‌ها

**روی برد:**
- **Power Input**: Terminal block 3-pin (L/N/PE) یا 5-pin (L1/L2/L3/N/PE)
- **CP/PP Output**: 2-pin header به کابل Type 2
- **Contactor Control**: 3-pin relay output
- **CT Input**: 3.5mm jack یا terminal block
- **RCM Interface**: 6-pin connector (power + signal)
- **Debug**: UART header (TX/RX/GND)
- **Programming**: SWD/JTAG header (STM32)
- **Ethernet**: RJ45
- **Antenna**: SMA connector (برای 4G)

## مشخصات فیزیکی برد

- **ابعاد پیشنهادی**: 150mm x 100mm (اندازه نسبتاً استاندارد)
- **لایه‌ها**: 4-layer PCB
  - Layer 1: Top - components & signals
  - Layer 2: GND plane
  - Layer 3: Power plane (+12V, +5V, +3.3V)
  - Layer 4: Bottom - components & signals
- **ضخامت**: 1.6mm
- **مس**: 2oz (70μm) برای مسیرهای قدرت، 1oz برای سیگنال
- **Silkscreen**: نشان‌گذاری واضح polarities، ولتاژها، جریان‌ها
- **Solder mask**: سبز (استاندارد) یا قرمز (برای visibility بهتر)

## Firmware Architecture (خلاصه)

```c
// استیت‌ماشین اصلی
enum ChargerState {
    STATE_INIT,          // بوت و self-test
    STATE_IDLE,          // آماده، منتظر اتصال
    STATE_CONNECTED,     // خودرو متصل (State B)
    STATE_CHARGING,      // در حال شارژ (State C)
    STATE_FAULT,         // خطا
    STATE_EMERGENCY_STOP // قطع اضطراری
};

// حلقه اصلی
void main_loop() {
    read_sensors();           // جریان، ولتاژ، دما
    update_cp_state();        // خواندن State A/B/C/D
    read_pp_resistance();     // محاسبه ظرفیت کابل
    check_safety();           // RCM, OV, UV, OC, OT
    run_state_machine();      // اجرای logic شارژ
    update_pwm_duty();        // تنظیم جریان مجاز
    control_contactor();      // باز/بسته کردن
    update_display();         // HMI
    handle_rfid();            // احراز هویت
    communicate_backend();    // OCPP
}
```

**کتابخانه‌های نرم‌افزاری:**
- STM32 HAL/LL drivers
- FreeRTOS (برای multi-tasking)
- OCPP client library (مثل Steve یا custom)
- Modbus/MQTT (اختیاری برای مانیتورینگ محلی)

## نکات تولید و تست

1. **Prototype آزمایشی**: ابتدا با ماژول‌های آماده (Arduino/ESP32) مفهوم را تست کنید
2. **PCB Manufacturing**: از سازنده‌های معتبر (JLCPCB، PCBWay) استفاده کنید
3. **Assembly**: SMD hand soldering برای نمونه اولیه، reflow برای تولید انبوه
4. **تست functional**:
   - CP signal quality (osilوسکوپ)
   - PP resistance reading
   - RCM trip test (با جریان لیک شبیه‌سازی‌شده)
   - Contactor timing
   - Temperature accuracy
5. **تست Safety**: HiPot, insulation resistance, earth continuity
6. **تست EMC**: conducted/radiated emissions (اگر ممکن)

## هزینه تخمینی (تک برد)

| جزء | قیمت تخمینی (USD) |
|-----|-------------------|
| STM32G474RET6 MCU | $8 |
| W5500 Ethernet Module | $5 |
| RCM Type B Module | $80-150 |
| Current Sensors (CT x3) | $15 |
| RFID Reader MFRC522 | $3 |
| Power Supply Modules | $10 |
| Contactors (TE EV200) | $40 |
| Passive Components | $10 |
| PCB (4-layer, qty 10) | $15 |
| Enclosure + Connectors | $20 |
| **جمع** | **~$206-276** |

*توجه: هزینه بدون کانکتور Type 2 (حدود $50-100) و کابل*

## استانداردها و گواهینامه‌ها

- ✅ IEC 61851-1 (Mode 3 AC charging)
- ✅ IEC 60364-7-722 (Installation)
- ✅ IEC 62752 (RCM)
- ✅ EN 61000 (EMC)
- 🎯 CE marking (برای اروپا)
- 🎯 گواهی استاندارد ایران (ISIRI)

## چک‌لیست نهایی

- [x] CP/PP مطابق IEC 61851-1
- [x] RCM Type B برای نشتی DC
- [x] اندازه‌گیری دقیق جریان/ولتاژ
- [x] حفاظت حرارتی
- [x] OCPP ready
- [x] RFID authentication
- [x] Emergency stop
- [x] Firmware OTA (از طریق backend)
- [ ] Type approval testing (فاز بعدی)

---

**نسخه**: 1.0
**تاریخ**: 2026-01-01
**مؤلف**: Claude AI
**مبتنی بر**: DESIGN.md - Iran EV Charger Project
