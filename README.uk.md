<img src="docs/images/icon.png" width="96" align="right" alt="FW_DisASM icon">

# FW_DisASM

[English](README.md) | **Українська** | [Deutsch](README.de.md)

[![Platform](https://img.shields.io/badge/platform-Windows-blue)](#вимоги)
[![.NET Framework](https://img.shields.io/badge/.NET%20Framework-4.0%2B-512BD4)](#вимоги)
[![Language](https://img.shields.io/badge/language-VB.NET-945DB7)](FW_DisASM)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![GitHub release](https://img.shields.io/github/v/release/DDN-Research/DisAssemblerARM)](https://github.com/DDN-Research/DisAssemblerARM/releases)

**FW_DisASM** — дизасемблер прошивок мікроконтролерів для Windows. Він читає файли
`.hex`, `.bin`, `.elf`, `.s19`, `.uf2` та образи застосунків ESP і перетворює машинний код
на зрозумілий лістинг асемблера з мітками, коментарями, посиланнями на рядки та назвами
регістрів периферії.

![Головне вікно FW_DisASM](docs/images/screenshot.png)

## Підтримувані архітектури

| Архітектура | Типові чипи |
|---|---|
| **ARM Thumb / Thumb-2** — ARMv6-M, ARMv7-M, ARMv7E-M (DSP), FPv4/FPv5, основа ARMv8-M | STM32 (F0…H7, G, L, U), GD32F, **RP2040**, RP2350 (ARM), nRF52, SAMD, LPC |
| **RISC-V RV32** — IMAFDC, Zicsr, Zifencei, Zba/Zbb/Zbs/Zbkb, Zcb, Zcmp | **ESP32-C2/C3/C6/H2/P4**, RP2350 (Hazard3), CH32V, GD32VF103 |
| **Xtensa** — LX6/LX7/LX106: core, density, windowed, loop, boolean, FPU, MUL/DIV | **ESP32**, ESP32-S2, ESP32-S3, ESP8266 |

## Підтримувані формати файлів

| Формат | Примітки |
|---|---|
| Intel HEX (`.hex`, `.ihx`) | типи записів 00–05, перевірка контрольних сум |
| Бінарний (`.bin`) | базова адреса **визначається автоматично** для Cortex-M і RP2040 |
| ELF32 (`.elf`, `.axf`, `.out`) | секції **і назви функцій** з таблиці символів |
| Motorola S-Record (`.s19`, `.srec`, `.mot`) | S1/S2/S3 |
| UF2 (`.uf2`) | RP2040/RP2350, ESP32-S2/S3, nRF52, SAMD, STM32 — чип визначається за family ID |
| Образ застосунку ESP (`.bin`, магічний байт `0xE9`) | сегменти, точка входу і тип чипа (ESP32, S2, S3, C2, C3, C6, H2, P4, ESP8266) |

## Можливості

* **Аналіз коду, а не просто лінійне дизасемблювання**
  * таблиця векторів Cortex-M → `Reset_Handler`, `HardFault_Handler`, `IRQn_Handler`, `Default_Handler`
  * рекурсивний обхід від точок входу відділяє код від даних
  * таблиці переходів `switch` (`TBB`/`TBH`, `adr + ldr pc, [rX, rY, lsl #2]`)
  * пули літералів (`LDR =…`, `L32R`, `AUIPC/LUI + ADDI`) зі значенням у коментарі
  * пошук функцій за прологами (`PUSH {…, lr}`, `ENTRY a1, N`, `addi sp, sp, -N`, `cm.push`)
  * хвостові та непрямі виклики через константи (`callx0`, `jalr`)
* мітки `sub_XXXXXXXX` / `loc_XXXXXXXX`, рядки ASCII, блоки `IT`, константи FPU
* назви периферії: STM32F0/F1/F3/F4, RP2040, ESP32, ESP32-C3/C6 і ядро Cortex-M (SCB, NVIC, SysTick…)
* інтерфейс: список функцій, перехід подвійним клацанням, пошук, перехід до адреси, HEX-перегляд, перетягування файлів
* режим командного рядка для пакетної обробки
* **інтерфейс українською та англійською** (меню *Мова / Language*)

Приклад (з [`examples/stm32f4_test.asm`](examples/stm32f4_test.asm)):

```
Reset_Handler:
08000054  4a1a        ldr      r2, [pc, #104]                 ; =0x40023000
08000056  481b        ldr      r0, [pc, #108]                 ; =0x080000D4 msg
08000058  f8d2 3830   ldr.w    r3, [r2, #2096]
0800005C  f043 0301   orr.w    r3, r3, #1
08000060  f8c2 3830   str.w    r3, [r2, #2096]
...
0800007A  eef8 6ae7   vcvt.f32.s32 s13, s15
08000084  eef5 7a00   vmov.f32 s15, #0.25
0800008C  eea6 6a87   vfma.f32 s12, s13, s14
```

## Швидкий старт

1. Завантажте `FW_DisASM.exe` зі сторінки [Releases](https://github.com/DDN-Research/DisAssemblerARM/releases) (встановлення не потрібне).
2. Запустіть і відкрийте файл прошивки (**Файл → Відкрити прошивку…** або перетягніть файл у вікно).
3. Читайте лістинг, користуйтеся списком функцій ліворуч, зберігайте через **Ctrl+S**.

Командний рядок:

```bat
FW_DisASM.exe firmware.hex -o firmware.asm
FW_DisASM.exe firmware.bin -base 0x08000000 -arch thumb -lang uk -o out.asm
```

## Вимоги

* Windows 7 або новіша з **.NET Framework 4.0** або новішим (у Windows 8/10/11 вже встановлено)
* Для збирання з вихідного коду: Visual Studio 2010 або новіша (Community — безкоштовна)

## Документація

| | English | Українська | Deutsch |
|---|---|---|---|
| Посібник користувача | [USER_GUIDE](docs/en/USER_GUIDE.md) | [Посібник користувача](docs/uk/USER_GUIDE.md) | [Benutzerhandbuch](docs/de/USER_GUIDE.md) |
| Збирання і публікація | [BUILDING](docs/en/BUILDING.md) | [Збирання і публікація](docs/uk/BUILDING.md) | [Erstellen & Veröffentlichen](docs/de/BUILDING.md) |
| Посібник розробника | [DEVELOPER_GUIDE](docs/en/DEVELOPER_GUIDE.md) | [Посібник розробника](docs/uk/DEVELOPER_GUIDE.md) | [Entwicklerhandbuch](docs/de/DEVELOPER_GUIDE.md) |

Вихідний код: <https://github.com/DDN-Research/DisAssemblerARM>

```bat
git clone https://github.com/DDN-Research/DisAssemblerARM.git
```

Див. також: [CHANGELOG](CHANGELOG.md) · [CONTRIBUTING](CONTRIBUTING.md) · [приклади](examples) · [тести](tests)

## Наскільки це точно?

Декодери автоматично звіряються з **GNU objdump** (див. [`tests/`](tests)):

| Набір | Результат |
|---|---|
| Thumb — **усі 65 536** 16-бітних кодів | 100 % збіг (крім архітектурно «непередбачуваних» кодувань) |
| Thumb-2 — 600 000 випадкових 32-бітних кодів | збіг на всіх визначених кодуваннях M-профілю; відмінності лише там, де objdump показує A-профіль/NEON або `<UNPREDICTABLE>` |
| RISC-V — усі 16-бітні C-коди + 200 000 32-бітних | збіг (крім зарезервованих режимів округлення та рідкісних CSR гіпервізора) |
| Xtensa — усі 16-бітні + 150 000 24-бітних | збіг на всіх інструкціях, відомих конфігурації LX106 |
| Реальна прошивка STM32F1 (37 КБ) після аналізу | **12 796 інструкцій — один в один з objdump** |

## Дизасемблер чи декомпілятор?

FW_DisASM видає **асемблер** (машинний код → асемблер). Отримання **коду на C**
(декомпіляція) — значно складніше завдання. Для нього використовуйте безкоштовну
[Ghidra](https://ghidra-sre.org/) (підтримує ARM Cortex-M, RISC-V і Xtensa) разом із плагіном
SVD-Loader для назв регістрів STM32. FW_DisASM зручно тримати поруч для швидкого огляду.

## Обмеження

* ARM: немає MVE/Helium (Cortex-M55/M85) і кастомних співпроцесорних інструкцій (CDE)
* Xtensa: MAC16 частково; SIMD ESP32-S3 (`EE.*`) не підтримується
* RISC-V: немає векторного розширення (V) і кастомних інструкцій ESP32-P4
* автовизначення базової адреси для `.bin` працює лише для Cortex-M і RP2040

## Ліцензія

[MIT](LICENSE)
