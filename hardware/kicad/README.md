# فایل‌های KiCad - طراحی PCB شارژرهای خودروی برقی

## نمای کلی

این پوشه شامل فایل‌های طراحی PCB برای دو نوع شارژر خودروی برقی است:

```
kicad/
├── AC-Wallbox/           # شارژر دیواری AC (7.4/22 kW)
│   ├── AC-Wallbox.kicad_pro       # فایل پروژه
│   ├── AC-Wallbox.kicad_sch       # شماتیک
│   └── AC-Wallbox.kicad_pcb       # طراحی PCB
│
└── DC-FastCharger/       # شارژر سریع DC (60-120 kW)
    ├── DC-FastCharger.kicad_pro   # فایل پروژه
    ├── DC-FastCharger.kicad_sch   # شماتیک
    └── DC-FastCharger.kicad_pcb   # طراحی PCB
```

---

## نصب KiCad

### ویندوز:
```
1. دانلود از: https://www.kicad.org/download/windows/
2. نصب نسخه 7.0 یا بالاتر
3. انتخاب تمام کامپوننت‌ها در حین نصب
```

### لینوکس (Ubuntu/Debian):
```bash
sudo add-apt-repository ppa:kicad/kicad-7.0-releases
sudo apt update
sudo apt install kicad
```

### macOS:
```bash
brew install kicad
```

---

## باز کردن پروژه‌ها

### روش 1: از طریق KiCad Manager
1. باز کردن KiCad
2. File → Open Project
3. انتخاب فایل `.kicad_pro`:
   - برای AC: `AC-Wallbox/AC-Wallbox.kicad_pro`
   - برای DC: `DC-FastCharger/DC-FastCharger.kicad_pro`

### روش 2: مستقیم
- دوبار کلیک روی فایل `.kicad_pro`

---

## ساختار فایل‌ها

### 1. فایل پروژه (`.kicad_pro`)
- تنظیمات پروژه (Design Rules، Net Classes، etc.)
- قوانین DRC (Design Rule Check)
- مشخصات لایه‌های PCB
- **Format**: JSON (قابل ویرایش با text editor)

### 2. فایل شماتیک (`.kicad_sch`)
- نمودار مداری الکتریکی
- اتصالات بین قطعات (nets)
- Hierarchical sheets
- **Format**: S-Expression (متن‌محور)

### 3. فایل PCB (`.kicad_pcb`)
- طراحی فیزیکی برد
- قرارگیری قطعات (Footprints)
- مسیرهای مسی (Traces)
- لایه‌های مختلف
- **Format**: S-Expression

---

## مشخصات فنی PCB

### AC Wallbox:
```
ابعاد:      150mm x 100mm
لایه‌ها:     4-layer
  - F.Cu:    سیگنال (Top)
  - In1.Cu:  GND Plane
  - In2.Cu:  Power Plane (+12V, +5V, +3.3V)
  - B.Cu:    سیگنال (Bottom)

ضخامت:     1.6mm
مس:         2oz (70μm) برای قدرت، 1oz برای سیگنال
Solder Mask: سبز (استاندارد)
Silkscreen: سفید
```

### DC Fast Charger:
```
ابعاد:      300mm x 200mm
لایه‌ها:     6-layer
  - F.Cu:    سیگنال (Top)
  - In1.Cu:  GND Plane
  - In2.Cu:  Power Plane (+48V, +12V)
  - In3.Cu:  سیگنال
  - In4.Cu:  سیگنال
  - B.Cu:    سیگنال (Bottom)

ضخامت:     2.0mm (برای عایق بهتر HV)
مس:         3oz (105μm) برای HV traces، 1oz برای سیگنال
Solder Mask: سبز با علائم خطر زرد
Silkscreen: سفید + WARNING labels
```

---

## ویرایش طراحی

### 1. ویرایش شماتیک

```bash
# باز کردن Schematic Editor
کلیک روی "Schematic Editor" در KiCad Manager
```

**عملیات اصلی:**
- `A`: افزودن سمبل (Add Symbol)
- `W`: رسم سیم (Wire)
- `P`: افزودن Power Symbol
- `L`: افزودن Label
- `M`: جابجایی (Move)
- `R`: چرخش (Rotate)
- `Del`: حذف

**تولید Netlist:**
```
Tools → Generate Netlist File
```

### 2. ویرایش PCB

```bash
# باز کردن PCB Editor
کلیک روی "PCB Editor" در KiCad Manager
```

**عملیات اصلی:**
- `X`: شروع Routing
- `V`: Via
- `M`: جابجایی قطعه
- `R`: چرخش
- `F`: Flip (پشت/رو)
- `Ctrl+B`: Fill Zones
- `Ctrl+Shift+Z`: Unfill Zones

**DRC Check:**
```
Inspect → Design Rules Checker → Run DRC
```

---

## تولید فایل‌های Gerber

### مرحله 1: تنظیمات
```
File → Plot...
```

### مرحله 2: انتخاب لایه‌ها

**برای 4-layer (AC):**
- ☑ F.Cu
- ☑ B.Cu
- ☑ In1.Cu (GND)
- ☑ In2.Cu (Power)
- ☑ F.SilkS
- ☑ B.SilkS
- ☑ F.Mask
- ☑ B.Mask
- ☑ Edge.Cuts

**برای 6-layer (DC):**
- همه لایه‌های بالا + In3.Cu + In4.Cu

### مرحله 3: تنظیمات Gerber
```
Format: Gerber X2
Include netlist attributes: ✓
Use Protel filename extensions: ✓ (برای سازگاری)
Subtract soldermask from silkscreen: ✓
```

### مرحله 4: تولید
```
1. کلیک "Plot"
2. کلیک "Generate Drill Files"
   - PTH and NPTH in single file: ✓
   - Drill Units: Millimeters
   - Zeros Format: Decimal
3. کلیک "Generate Drill File"
```

### مرحله 5: فایل‌های خروجی
```
gerber/
├── AC-Wallbox-F_Cu.gbr         # Top copper
├── AC-Wallbox-In1_Cu.gbr       # Inner layer 1 (GND)
├── AC-Wallbox-In2_Cu.gbr       # Inner layer 2 (Power)
├── AC-Wallbox-B_Cu.gbr         # Bottom copper
├── AC-Wallbox-F_SilkS.gbr      # Top silkscreen
├── AC-Wallbox-B_SilkS.gbr      # Bottom silkscreen
├── AC-Wallbox-F_Mask.gbr       # Top solder mask
├── AC-Wallbox-B_Mask.gbr       # Bottom solder mask
├── AC-Wallbox-Edge_Cuts.gbr    # Board outline
├── AC-Wallbox.drl              # Drill file
└── AC-Wallbox-job.gbrjob       # Gerber job file
```

---

## سفارش PCB

### تأمین‌کنندگان پیشنهادی:

#### 1. JLCPCB (چین - ارزان و سریع)
```
وب‌سایت: https://jlcpcb.com
قیمت نمونه: $2 برای 5 عدد PCB (2-layer)
            $5-15 برای 4-layer
            $25-50 برای 6-layer
زمان: 2-3 روز ساخت + 5-7 روز ارسال
```

**آپلود:**
1. فشرده کردن پوشه gerber: `gerber.zip`
2. آپلود در سایت JLCPCB
3. بررسی خودکار فایل‌ها
4. انتخاب مشخصات:
   - PCB Qty: 5
   - PCB Thickness: 1.6mm (AC) یا 2.0mm (DC)
   - PCB Color: Green
   - Surface Finish: HASL
   - Copper Weight: 2oz (برای قدرت)

#### 2. PCBWay (چین - کیفیت بالاتر)
```
وب‌سایت: https://www.pcbway.com
قیمت: کمی گران‌تر از JLCPCB اما کیفیت بهتر
زمان: مشابه JLCPCB
```

#### 3. AllPCB (چین)
```
وب‌سایت: https://www.allpcb.com
مزیت: قیمت رقابتی، پشتیبانی خوب
```

#### 4. سازندگان ایرانی
```
- شرکت‌های PCB در تهران (جمهوری)
- زمان: 5-10 روز
- قیمت: معمولاً گران‌تر از چین
- مزیت: عدم نیاز به واردات
```

---

## بررسی فایل‌های Gerber

### آنلاین (رایگان):
```
https://www.gerber-viewer.com
https://gerbv.github.io (نرم‌افزار دسکتاپ)
```

### در KiCad:
```
Tools → View Gerber Files (Gerbview)
```

**چک‌لیست بررسی:**
- ☑ تمام لایه‌ها موجود هستند
- ☑ Board outline صحیح است
- ☑ Drill holes در جای درست
- ☑ Silkscreen خوانا است
- ☑ هیچ short/open نیست
- ☑ Clearance ها رعایت شده

---

## تبدیل به فرمت‌های دیگر

### به Altium Designer:
```
1. فایل KiCad را در KiCad باز کنید
2. File → Export → Altium Project
3. یا استفاده از ابزار تبدیل آنلاین:
   https://www.snapeda.com/kicad-to-altium/
```

### به Eagle:
```
1. File → Export → Eagle CAD
2. یا استفاده از:
   https://github.com/lachlanA/eagle-to-kicad
```

### به EasyEDA:
```
1. EasyEDA می‌تواند مستقیم Gerber import کند
2. یا convert manual با کپی کردن netlist
```

---

## کتابخانه‌های قطعات

### نصب کتابخانه‌های اضافی:

```bash
# Clone کتابخانه رسمی KiCad
git clone https://github.com/KiCad/kicad-symbols.git
git clone https://github.com/KiCad/kicad-footprints.git
```

### کتابخانه‌های مفید برای این پروژه:

1. **STM32 MCU:**
   - در کتابخانه پیش‌فرض KiCad موجود است
   - Library: MCU_ST_STM32G4, MCU_ST_STM32F7

2. **Connectors:**
   - Phoenix Contact: در Connector library
   - Anderson PowerPole: دانلود از SnapEDA

3. **Power Components:**
   - از کتابخانه Device (R, C, L)
   - MOSFETs: از Device یا Transistor_FET

4. **سایت‌های دانلود Footprint:**
```
https://www.snapeda.com        # پایگاه داده بزرگ قطعات
https://componentsearchengine.com
https://www.samacsys.com
```

---

## نکات مهم طراحی

### برای AC Wallbox:

1. **جداسازی قدرت/سیگنال:**
   - فاصله حداقل 5mm بین بخش AC و بخش کنترل
   - استفاده از optocoupler برای ایزولاسیون

2. **Grounding:**
   - GND plane یکپارچه روی In1.Cu
   - Single point ground connection

3. **Trace Width:**
   - AC power (40A): حداقل 3mm (با 2oz copper)
   - سیگنال: 0.25mm
   - Power rails: 1-2mm

### برای DC Fast Charger:

1. **HIGH VOLTAGE Safety:**
   - فاصله 5mm از HV traces
   - Keepout zones مشخص
   - Warning labels روی silkscreen

2. **Trace Width برای HV:**
   - DC+ / DC- (400A): 15mm یا بیشتر
   - با copper 3oz (105μm)
   - یا استفاده از copper pour

3. **Via Stitching:**
   - برای GND plane: via هر 5mm
   - برای EMI shielding

---

## عیب‌یابی

### مشکل: DRC Errors زیاد

**راه‌حل:**
```
1. Inspect → Design Rules Checker
2. بررسی errors:
   - Clearance: افزایش فاصله traces
   - Track width: اصلاح width
   - Unconnected: اتمام routing
```

### مشکل: Footprint پیدا نمی‌شود

**راه‌حل:**
```
1. Preferences → Manage Footprint Libraries
2. Add Library → انتخاب پوشه
3. یا دانلود از SnapEDA
```

### مشکل: Gerber تولید نمی‌شود

**راه‌حل:**
```
1. Check file permissions
2. Plot به پوشه دیگری
3. بستن سایر برنامه‌ها که فایل باز دارند
```

---

## منابع یادگیری

### آموزش‌های رسمی KiCad:
```
https://docs.kicad.org/
https://www.youtube.com/c/kicadpcb
```

### کتاب‌ها:
```
- "KiCad Like a Pro" by Peter Dalmaris
- "Getting to Blinky" (KiCad tutorial series)
```

### انجمن‌ها:
```
https://forum.kicad.info/
https://www.reddit.com/r/KiCad/
```

---

## مجوز و استفاده

این فایل‌های طراحی تحت مجوز MIT منتشر شده‌اند:

```
✓ استفاده تجاری مجاز
✓ تغییر و اصلاح مجاز
✓ توزیع مجاز
✗ بدون ضمانت
```

---

## تماس و پشتیبانی

برای سؤالات فنی:
- GitHub Issues: [لینک repository]
- Email: [ایمیل پروژه]

---

**نسخه**: 1.0
**تاریخ**: 2026-01-01
**پروژه**: Iran EV Charger
**مؤلف**: Claude AI

**⚠️ هشدار**: این طراحی‌ها با ولتاژ بالا (تا 800V DC) کار می‌کنند. فقط توسط افراد مجرب استفاده شود.
