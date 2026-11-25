<img src="docs/images/icon.png" width="96" align="right" alt="FW_DisASM icon">

# FW_DisASM

[English](README.md) | [Українська](README.uk.md) | **Deutsch**

[![Platform](https://img.shields.io/badge/platform-Windows-blue)](#voraussetzungen)
[![.NET Framework](https://img.shields.io/badge/.NET%20Framework-4.0%2B-512BD4)](#voraussetzungen)
[![Language](https://img.shields.io/badge/language-VB.NET-945DB7)](FW_DisASM)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![GitHub release](https://img.shields.io/github/v/release/DDN-Research/DisAssemblerARM)](https://github.com/DDN-Research/DisAssemblerARM/releases)

**FW_DisASM** ist ein Disassembler für Mikrocontroller-Firmware unter Windows. Er liest
`.hex`-, `.bin`-, `.elf`-, `.s19`- und `.uf2`-Dateien sowie ESP-Anwendungsabbilder und wandelt
den Maschinencode in ein gut lesbares Assembler-Listing mit Labels, Kommentaren,
String-Referenzen und Namen der Peripherieregister um.

![FW_DisASM Hauptfenster](docs/images/screenshot.png)

## Unterstützte Architekturen

| Architektur | Typische Chips |
|---|---|
| **ARM Thumb / Thumb-2** — ARMv6-M, ARMv7-M, ARMv7E-M (DSP), FPv4/FPv5, ARMv8-M-Basis | STM32 (F0…H7, G, L, U), GD32F, **RP2040**, RP2350 (ARM), nRF52, SAMD, LPC |
| **RISC-V RV32** — IMAFDC, Zicsr, Zifencei, Zba/Zbb/Zbs/Zbkb, Zcb, Zcmp | **ESP32-C2/C3/C6/H2/P4**, RP2350 (Hazard3), CH32V, GD32VF103 |
| **Xtensa** — LX6/LX7/LX106: Core, Density, Windowed, Loop, Boolean, FPU, MUL/DIV | **ESP32**, ESP32-S2, ESP32-S3, ESP8266 |

## Unterstützte Dateiformate

| Format | Hinweise |
|---|---|
| Intel HEX (`.hex`, `.ihx`) | Satztypen 00–05, Prüfsummen werden kontrolliert |
| Binärdatei (`.bin`) | Basisadresse wird für Cortex-M- und RP2040-Abbilder **automatisch erkannt** |
| ELF32 (`.elf`, `.axf`, `.out`) | Sektionen **und Funktionsnamen** aus der Symboltabelle |
| Motorola S-Record (`.s19`, `.srec`, `.mot`) | S1/S2/S3 |
| UF2 (`.uf2`) | RP2040/RP2350, ESP32-S2/S3, nRF52, SAMD, STM32 — Chip wird über die Family-ID erkannt |
| ESP-Anwendungsabbild (`.bin`, Magic-Byte `0xE9`) | Segmente, Einsprungadresse und Chiptyp (ESP32, S2, S3, C2, C3, C6, H2, P4, ESP8266) |

## Funktionen

* **Codeanalyse statt reinem linearem Disassemblieren**
  * Cortex-M-Vektortabelle → `Reset_Handler`, `HardFault_Handler`, `IRQn_Handler`, `Default_Handler`
  * rekursive Verfolgung ab den Einsprungpunkten trennt Code von Daten
  * `switch`-Sprungtabellen (`TBB`/`TBH`, `adr + ldr pc, [rX, rY, lsl #2]`)
  * Literal-Pools (`LDR =…`, `L32R`, `AUIPC/LUI + ADDI`) mit dem geladenen Wert im Kommentar
  * Funktionserkennung anhand von Prologen (`PUSH {…, lr}`, `ENTRY a1, N`, `addi sp, sp, -N`, `cm.push`)
  * Tail-Calls und indirekte Aufrufe über Konstanten (`callx0`, `jalr`)
* Labels `sub_XXXXXXXX` / `loc_XXXXXXXX`, ASCII-Strings, `IT`-Blöcke, FPU-Konstanten
* Peripherienamen: STM32F0/F1/F3/F4, RP2040, ESP32, ESP32-C3/C6 und Cortex-M-Kern (SCB, NVIC, SysTick…)
* Oberfläche: Funktionsliste, Navigation per Doppelklick, Suche, Sprung zu Adresse, HEX-Ansicht, Drag & Drop
* Kommandozeilenmodus für die Stapelverarbeitung
* **Benutzeroberfläche auf Ukrainisch und Englisch** (Menü *Мова / Language*)

Beispiel (aus [`examples/stm32f4_test.asm`](examples/stm32f4_test.asm)):

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

## Schnellstart

1. `FW_DisASM.exe` von der Seite [Releases](https://github.com/DDN-Research/DisAssemblerARM/releases) herunterladen (keine Installation nötig).
2. Starten und eine Firmware-Datei öffnen (**File → Open firmware…** oder per Drag & Drop).
3. Listing lesen, die Funktionsliste links nutzen, mit **Strg+S** speichern.

Kommandozeile:

```bat
FW_DisASM.exe firmware.hex -o firmware.asm
FW_DisASM.exe firmware.bin -base 0x08000000 -arch thumb -lang en -o out.asm
```

> Die Programmoberfläche ist auf Ukrainisch und Englisch verfügbar; die Dokumentation zusätzlich auf Deutsch.

## Voraussetzungen

* Windows 7 oder neuer mit **.NET Framework 4.0** oder höher (unter Windows 8/10/11 bereits vorhanden)
* Zum Erstellen aus dem Quellcode: Visual Studio 2010 oder neuer (die Community Edition ist kostenlos)

## Dokumentation

| | English | Українська | Deutsch |
|---|---|---|---|
| Benutzerhandbuch | [USER_GUIDE](docs/en/USER_GUIDE.md) | [Посібник користувача](docs/uk/USER_GUIDE.md) | [Benutzerhandbuch](docs/de/USER_GUIDE.md) |
| Erstellen & Veröffentlichen | [BUILDING](docs/en/BUILDING.md) | [Збирання і публікація](docs/uk/BUILDING.md) | [Erstellen & Veröffentlichen](docs/de/BUILDING.md) |
| Entwicklerhandbuch | [DEVELOPER_GUIDE](docs/en/DEVELOPER_GUIDE.md) | [Посібник розробника](docs/uk/DEVELOPER_GUIDE.md) | [Entwicklerhandbuch](docs/de/DEVELOPER_GUIDE.md) |

Quellcode: <https://github.com/DDN-Research/DisAssemblerARM>

```bat
git clone https://github.com/DDN-Research/DisAssemblerARM.git
```

Siehe auch: [CHANGELOG](CHANGELOG.md) · [CONTRIBUTING](CONTRIBUTING.md) · [Beispiele](examples) · [Tests](tests)

## Wie genau ist das Ergebnis?

Die Decoder werden automatisch gegen **GNU objdump** geprüft (siehe [`tests/`](tests)):

| Testsatz | Ergebnis |
|---|---|
| Thumb — **alle 65.536** 16-Bit-Kodierungen | 100 % Übereinstimmung (außer architektonisch UNPREDICTABLE-Kodierungen) |
| Thumb-2 — 600.000 zufällige 32-Bit-Kodierungen | Übereinstimmung bei allen definierten M-Profil-Kodierungen; Abweichungen nur dort, wo objdump A-Profil/NEON oder `<UNPREDICTABLE>` dekodiert |
| RISC-V — alle 16-Bit-C-Kodierungen + 200.000 32-Bit | Übereinstimmung (außer reservierten Rundungsmodi und seltenen Hypervisor-CSR-Namen) |
| Xtensa — alle 16-Bit + 150.000 24-Bit | Übereinstimmung bei allen Befehlen, die die LX106-Konfiguration kennt |
| Echte STM32F1-Firmware (37 KB) nach der Analyse | **12.796 Befehle identisch** mit objdump |

## Disassembler oder Decompiler?

FW_DisASM erzeugt **Assembler** (Maschinencode → Assembler). Die Rückgewinnung von
**C-Code** (Dekompilierung) ist eine deutlich größere Aufgabe. Dafür eignet sich das kostenlose
[Ghidra](https://ghidra-sre.org/) (unterstützt ARM Cortex-M, RISC-V und Xtensa) zusammen mit dem
Plugin SVD-Loader für STM32-Registernamen. FW_DisASM ist daneben praktisch für einen schnellen Überblick.

## Einschränkungen

* ARM: kein MVE/Helium (Cortex-M55/M85) und keine benutzerdefinierten Coprozessor-Befehle (CDE)
* Xtensa: MAC16 nur teilweise; ESP32-S3-SIMD (`EE.*`) wird nicht unterstützt
* RISC-V: keine Vektorerweiterung (V) und keine ESP32-P4-spezifischen Befehle
* die automatische Basisadresserkennung für `.bin` funktioniert nur bei Cortex-M- und RP2040-Abbildern

## Lizenz

[MIT](LICENSE)
