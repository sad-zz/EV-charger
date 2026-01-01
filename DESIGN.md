# EV Charger Design (Iran Market) — AC Home + DC Fast (CCS2)

## محصول‌ها
- AC Wallbox: 7.4 kW تک‌فاز یا 22 kW سه‌فاز، Type 2، IEC 61851-1.
- DC Fast: 60 kW (stackable تا 120 kW)، خروجی 200–750(950) V، CCS2 (قابل افزودن GB/T).

## الزامات کلیدی
- کانکتور/پروتکل: AC → Type 2, CP/PP PWM (IEC 61851-1) | DC → CCS2 (IEC 61851-23/-24 + ISO 15118/DIN 70121), ولتاژ 200–750(950) V.
- ایمنی: SPD، MCB/RCBO، RCM Type B، RISO (IMD)، pre-charge، کنتاکتور interlock، سنسور دما کابل/کانکتور، DC leakage detection.
- شبکه/مدیریت: OCPP 1.6J (حداقل)، RFID، اترنت/4G، لاگ خطا، امکان پرداخت محلی (Wallet/USSD).

## معماری AC Wallbox (7.4/22 kW)
- ورودی: AC تک/سه‌فاز، فیلتر EMI، SPD، MCB/RCBO.
- حفاظت نشتی: RCM Type B (ماژول آماده).
- قدرت: کنتاکتور AC، سنسور جریان (CT/Hall)، شنت/ولتاژ برای اندازه‌گیری.
- کنترل: MCU (STM32/ESP32) برای CP/PP PWM، اندازه‌گیری جریان/ولتاژ، درایو کنتاکتور، خطاها.
- HMI/Backend: LED/نمایشگر کوچک، RFID، LAN/4G، OCPP اختیاری.
- کانکتور: سوکت یا کابل Type 2 (تأییدشده، CE/TÜV).

## معماری DC Fast (60 kW → 120 kW)
1) ورودی و حفاظت: سه‌فاز 400 Vac، فیلتر EMI، SPD، MCB، کنتاکتور اصلی.
2) PFC + DC Bus: ~800 Vdc.
3) DC/DC ایزوله ماژولار: ماژول‌های 20–30 kW stackable (مثلاً 3× برای 60 kW، 4× برای 80–120 kW) خروجی 200–750(950) V.
4) حسگرها/ایمنی: جریان DC (Hall/fluxgate)، ولتاژ باس/خروجی، دمای ماژول/کابل/کانکتور، RISO (IMD)، DC leakage، pre-charge، کنتاکتور اصلی/بای‌پس.
5) کنترل قدرت: MCU ایمنی + DSP/MCU قدرت، استیت‌ماشین Pre-charge/Run/Fault، ramp current.
6) پروتکل CCS2: ماژول PLC Green PHY + استک ISO 15118/DIN 70121 آماده (کاهش زمان توسعه).
7) HMI/Backend: SOM صنعتی (i.MX/STM32MP1/CM4 صنعتی) برای OCPP 1.6J/2.0.1، نمایشگر، RFID، LAN/4G.
8) کابل/کانکتور: CCS2 تأییدشده؛ برای >80–100 A بررسی کابل خنک‌شونده.

## نسخه دوگانه CCS2 + GB/T (اختیاری)
- کانکتور GB/T DC + پروتکل CAN (GBT 27930)، interlock نرم‌افزاری/مکانیکی برای جلوگیری از هم‌زمانی.

## فلو استیت (AC)
1) Standby: خودآزمایی، RCM OK.
2) Plug detect (PP)، CP PWM set → advertise current.
3) Ready → کنتاکتور AC بسته.
4) Charging: مانیتور جریان/ولتاژ/دما؛ خطاها: OC/OT/RCM/UV/OV.
5) Stop/Unplug: کنتاکتور باز، تخلیه امن.

## فلو استیت (DC CCS2)
1) Standby: RISO/IMD، سلامت سیستم.
2) Plug detect (PP/CP)، Handshake DIN 70121/ISO 15118.
3) Pre-charge: کنتاکتور پیش‌شارژ، نزدیک‌کردن ولتاژ خروجی به باتری.
4) Main contactor ON → ramp current به مقدار درخواستی.
5) Monitoring: OV/UV/OC/OT/RISO/Leakage/PLC loss.
6) Stop/Unplug/Fault: ramp-down، باز کردن کنتاکتورها، discharge.

## BoM سطح بالا (نمونه تجاری برای تسریع)
- PLC/ISO 15118: ماژول آماده (A2B/ChargeByte/Vector EVSE-PLC).
- کنترلر مرکزی: SOM صنعتی (i.MX/STM32MP1/CM4 صنعتی) + MCU ایمنی جدا.
- ماژول قدرت DC: 20–30 kW ایزوله، 200–750(950) V stackable.
- RCM Type B: ماژول تأییدشده (Bender/Doepke).
- IMD/RISO: ماژول صنعتی (مثلاً Bender iso...).
- سنسور جریان DC: Hall/fluxgate دقیق (LEM/Allegro).
- کانکتور/کابل: Type 2 (AC)، CCS2 (DC) تأییدشده؛ خنک‌شونده در توان بالاتر.
- حفاظت: SPD کلاس II، MCB/RCBO مناسب جریان، کنتاکتورهای AC/DC با کویل کم‌مصرف.
- حرارتی/EMC: هیت‌سینک/فن/کانال هوا، فیلتر EMI ورودی، شیلدینگ کابل سیگنال.

## آزمون و انطباق
- IEC 61851-1 (AC)، IEC 61851-23/-24 (DC)، IEC 60364-7-722.
- تست‌های: HiPot، RISO، Leakage (AC/DC)، Functional (pre-charge، قطع خطا)، EMC (پایه).

## نقشه راه
- فاز 1 (AC): طراحی برد کنترل CP/PP + RCM Type B، نمونه 7.4/22 kW، تست میدانی.
- فاز 2 (DC 60 kW): انتخاب ماژول قدرت و PLC ماژولار، کنترلر دوگانه (MCU ایمنی + SOM)، نمونه میدانی.
- فاز 3 (ارتقا 120 kW + GB/T اختیاری): افزودن ماژول قدرت، کابل خنک‌شونده در صورت نیاز، پروتکل GB/T CAN.