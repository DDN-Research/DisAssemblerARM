<img src="docs/images/icon.png" width="96" align="right" alt="FW_DisASM icon">

# FW_DisASM

**English** | [Українська](README.uk.md) | [Deutsch](README.de.md)

[![Platform](https://img.shields.io/badge/platform-Windows-blue)](#requirements)
[![.NET Framework](https://img.shields.io/badge/.NET%20Framework-4.0%2B-512BD4)](#requirements)
[![Language](https://img.shields.io/badge/language-VB.NET-945DB7)](FW_DisASM)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![GitHub release](https://img.shields.io/github/v/release/DDN-Research/DisAssemblerARM)](https://github.com/DDN-Research/DisAssemblerARM/releases)

**FW_DisASM** is a Windows disassembler for microcontroller firmware. It reads
`.hex`, `.bin`, `.elf`, `.s19`, `.uf2` and ESP application images and turns the
machine code into a readable assembly listing with labels, comments, string
references and peripheral register names.

![FW_DisASM main window](docs/images/screenshot.png)

## Supported architectures

| Architecture | Typical chips |
|---|---|
| **ARM Thumb / Thumb-2** — ARMv6-M, ARMv7-M, ARMv7E-M (DSP), FPv4/FPv5, core ARMv8-M | STM32 (F0…H7, G, L, U), GD32F, **RP2040**, RP2350 (ARM), nRF52, SAMD, LPC |
| **RISC-V RV32** — IMAFDC, Zicsr, Zifencei, Zba/Zbb/Zbs/Zbkb, Zcb, Zcmp | **ESP32-C2/C3/C6/H2/P4**, RP2350 (Hazard3), CH32V, GD32VF103 |
| **Xtensa** — LX6/LX7/LX106: core, density, windowed, loop, boolean, FPU, MUL/DIV | **ESP32**, ESP32-S2, ESP32-S3, ESP8266 |

## Supported file formats

| Format | Notes |
|---|---|
| Intel HEX (`.hex`, `.ihx`) | record types 00–05, checksums are verified |
| Raw binary (`.bin`) | base address is **auto-detected** for Cortex-M and RP2040 images |
| ELF32 (`.elf`, `.axf`, `.out`) | sections **and function names** from the symbol table |
| Motorola S-Record (`.s19`, `.srec`, `.mot`) | S1/S2/S3 |
| UF2 (`.uf2`) | RP2040/RP2350, ESP32-S2/S3, nRF52, SAMD, STM32 — chip detected from the family ID |
| ESP application image (`.bin`, magic `0xE9`) | segments, entry point and chip type (ESP32, S2, S3, C2, C3, C6, H2, P4, ESP8266) |

## Features

* **Code analysis instead of a plain linear dump**
  * Cortex-M vector table → `Reset_Handler`, `HardFault_Handler`, `IRQn_Handler`, `Default_Handler`
  * recursive traversal from the entry points separates code from data
  * `switch` jump tables (`TBB`/`TBH`, `adr + ldr pc, [rX, rY, lsl #2]`)
  * literal pools (`LDR =…`, `L32R`, `AUIPC/LUI + ADDI`) with the loaded value in a comment
  * function discovery by prologue (`PUSH {…, lr}`, `ENTRY a1, N`, `addi sp, sp, -N`, `cm.push`)
  * tail calls and indirect calls through constants (`callx0`, `jalr`)
* `sub_XXXXXXXX` / `loc_XXXXXXXX` labels, ASCII strings, `IT` blocks, FPU constants
* peripheral names: STM32F0/F1/F3/F4, RP2040, ESP32, ESP32-C3/C6 and the Cortex-M core (SCB, NVIC, SysTick…)
* GUI: function list, double-click navigation, search, go to address, HEX view, drag & drop
* command line mode for batch processing
* **user interface in Ukrainian and English** (menu *Мова / Language*)

Example (from [`examples/stm32f4_test.asm`](examples/stm32f4_test.asm)):

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

## Quick start

1. Download `FW_DisASM.exe` from the [Releases](https://github.com/DDN-Research/DisAssemblerARM/releases) page (no installation needed).
2. Run it and open a firmware file (**File → Open firmware…** or drag & drop).
3. Read the listing, use the function list on the left, save with **Ctrl+S**.

Command line:

```bat
FW_DisASM.exe firmware.hex -o firmware.asm
FW_DisASM.exe firmware.bin -base 0x08000000 -arch thumb -lang en -o out.asm
```

## Requirements

* Windows 7 or newer with **.NET Framework 4.0** or later (pre-installed on Windows 8/10/11)
* To build from source: Visual Studio 2010 or newer (Community edition is free)

## Documentation

| | English | Українська | Deutsch |
|---|---|---|---|
| User guide | [USER_GUIDE](docs/en/USER_GUIDE.md) | [Посібник користувача](docs/uk/USER_GUIDE.md) | [Benutzerhandbuch](docs/de/USER_GUIDE.md) |
| Building & releasing | [BUILDING](docs/en/BUILDING.md) | [Збирання і публікація](docs/uk/BUILDING.md) | [Erstellen & Veröffentlichen](docs/de/BUILDING.md) |
| Developer guide | [DEVELOPER_GUIDE](docs/en/DEVELOPER_GUIDE.md) | [Посібник розробника](docs/uk/DEVELOPER_GUIDE.md) | [Entwicklerhandbuch](docs/de/DEVELOPER_GUIDE.md) |

Source code: <https://github.com/DDN-Research/DisAssemblerARM>

```bat
git clone https://github.com/DDN-Research/DisAssemblerARM.git
```

See also: [CHANGELOG](CHANGELOG.md) · [CONTRIBUTING](CONTRIBUTING.md) · [examples](examples) · [tests](tests)

## How accurate is it?

The decoders are verified automatically against **GNU objdump** (see [`tests/`](tests)):

| Test set | Result |
|---|---|
| Thumb — **all 65,536** 16-bit encodings | 100 % match (except architecturally UNPREDICTABLE encodings) |
| Thumb-2 — 600,000 random 32-bit encodings | match on all defined M-profile encodings; differences only where objdump decodes A-profile/NEON or `<UNPREDICTABLE>` |
| RISC-V — all 16-bit C encodings + 200,000 32-bit | match (except reserved rounding modes and rare hypervisor CSR names) |
| Xtensa — all 16-bit + 150,000 24-bit | match on every instruction known to the LX106 configuration |
| Real STM32F1 firmware (37 KB) after analysis | **12,796 instructions identical** to objdump |

## Disassembler vs. decompiler

FW_DisASM produces **assembly** (machine code → assembler). Getting **C code** back
(decompilation) is a much larger task. For that, use the free
[Ghidra](https://ghidra-sre.org/) (supports ARM Cortex-M, RISC-V and Xtensa) together with
the SVD-Loader plugin for STM32 register names — FW_DisASM is handy next to it as a fast overview.

## Limitations

* ARM: no MVE/Helium (Cortex-M55/M85) and no custom coprocessor instructions (CDE)
* Xtensa: MAC16 is partial; ESP32-S3 SIMD (`EE.*`) is not supported
* RISC-V: no vector extension (V) and no ESP32-P4 custom instructions
* automatic base address detection for raw `.bin` works for Cortex-M and RP2040 images only

## License

[MIT](LICENSE)
