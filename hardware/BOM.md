# PCB_V10 populated-board BOM

The photographed board carries a **1 MHz CTS oscillator at X2**, a **150 ns Atmel EEPROM at U5**, and a **55 ns Alliance SRAM at U6**. The 8 MHz timing case in the [design review](easyeda/README.md) applies to the archived design's oscillator choice. The photos identify the installed oscillator's rating; the clock delivered to U1 also depends on H5 and any external clock.

## Parts identified on the board

Each row represents one fitted part. The marking column preserves the identifying text from the package. Camera filenames are listed below the tables.

| Ref | Function | Manufacturer | Fitted part number | Package marking or identification |
| --- | --- | --- | --- | --- |
| U1 | 65C02 CPU | Western Design Center | W65C02S6TPG-14 | WDC · `W65C02S6TPG-14` |
| U2 | 6522 VIA | Western Design Center | W65C22S6TPG-14 | WDC · `W65C22S6TPG-14` |
| U3 | Address decoder | Lattice Semiconductor | GAL22V10D-25LP | Lattice logo · `GAL22V10D` / `25LP` |
| U4 | 6551 serial ACIA | Western Design Center | W65C51N6TPG-14 | WDC · `W65C51N6TPG-14` |
| U5 | 32 KiB parallel EEPROM | Atmel | AT28C256-15PU | `AT28C256` / `15U` in a 28-pin PDIP. The package and 150 ns marking give the ordering code. [Microchip marking and ordering tables](https://ww1.microchip.com/downloads/aemDocuments/documents/MPD/ProductDocuments/DataSheets/AT28C256-Industrial-Grade-256-Kbit-Paged-Parallel-EEPROM-Data-Sheet-DS20006386.pdf) |
| U6 | 32 KiB SRAM | Alliance Memory | AS6C62256-55PCN | `AS6C62256` / `-55PCN` |
| U8 | Quad NAND gate for IRQ logic | Xinluda | XD74LS00 | `XINLUDA` / `XD74LS00`, 14-pin DIP. EasyEDA lists TI SN74LS00N. |
| U9 | Keyboard controller | Microchip | PIC16F1826-E/P | `PIC16F1826` / `-E/P`. EasyEDA lists the -I/P grade. |
| X2 | CPU clock oscillator | CTS | MXO45HST-3C-1M000000 | `MXO45HST` / `1M000000` / `CTS 3C`. EasyEDA lists YXC O1212D8MFDA4SC, 8 MHz. |

## Other board positions

Quantities below count positions in PCB_V10. Values and catalog numbers in the **design specification** column come from EasyEDA. Treat those numbers as purchasing references until a package marking, measurement, or assembly record confirms the fitted part. A visible package alone cannot establish capacitance, LED type, or connector manufacturer.

| References | Qty | Design specification | What the photos establish |
| --- | ---: | --- | --- |
| X1 | 1 | 1.8432 MHz ACIA crystal · X49UD18432MSD2SC-1 | Metal-can crystal fitted near U4. The frequency marking begins `1.84`; the full marking and ordering code are unresolved. |
| R1–R3, R80, R81 | 5 | 1 kΩ · RS-05K1001FT | R1 and R3 are visible beside RESET. Exact fitted numbers unverified. |
| R4 | 1 | 1 MΩ axial resistor | Blue axial resistor fitted beside X1. Full band sequence unclear. |
| R5, R60–R64, R75–R79 | 11 | 220 Ω · 0805W8J0221T5E | Resistors visible by READY/TAKEN and the VIA indicator bank. Exact fitted numbers unverified. |
| R6–R10, R59 | 6 | Schematic **220 Ω**. PCB **1 kΩ**. Schematic purchasing code RS-05K1001FT (1 kΩ). | The POWER and ACIA resistor markings show `22…`. The final digit is unclear. The photos do not settle the six fitted values. |
| R11, R14, R16, R18, R20, R22, R24, R26, R27, R29, R31, R33, R35, R37, R39, R42, R43, R45, R47, R49, R51, R53, R55, R58, R65, R67, R69, R71, R74 | 29 | 3.3 kΩ · 0805W8F3301T5E | FPGA divider series resistors. Individual markings unverified. |
| R12, R13, R15, R17, R19, R21, R23, R25, R28, R30, R32, R34, R36, R38, R40, R41, R44, R46, R48, R50, R52, R54, R56, R57, R66, R68, R70, R72, R73 | 29 | 2.2 kΩ · 0805W8F2201T5E | FPGA divider shunt resistors. Individual markings unverified. |
| C1 | 1 | 30 pF · CL10C300JB8NNNC | SMD component fitted beside X1. Capacitance unreadable. |
| C2, C4 | 2 | 10 µF polarized · EGW2DM100F16OT | Two radial electrolytics visible. Sleeve markings do not establish the fitted capacitance or part number. |
| C3, C5–C9 | 6 | 100 nF · CC1H104ZA1PD3F5P3003 | Orange through-hole capacitors visible at several ICs. Face markings unreadable. |
| LED1–LED16, READY, TAKEN | 18 | Indicator LEDs · 19-217/R6C-AL1M2VY/3T | Multiple SMD indicators visible. Printed board labels identify their functions. Individual LED part numbers and emitted colors are unverified. |
| KEY1 | 1 | Reset switch · K2-1107ST-A4SW-06 | Pushbutton fitted beside CPU. |
| H1, H2 | 2 | 1×6 serial and PIC programming headers · LCSC C37208 and C124380 respectively | Both positions identifiable. Connector part numbers unverified. |
| H3, H8–H10 | 4 | Single-pin SYNC, NMIB, RDY and BE headers · PZ254V-11-01P | Test pins visible along the CPU edge. |
| H4, H5 | 2 | 1×2 power and clock headers · LCSC C124375 | Power pins and clock-select pins visible. |
| H6 | 1 | 2×16 bus header · DS1023-2*16SF11 | Dual-row header visible at the bus interface. |
| H7 | 1 | 2×17 VGA-controller header · DS1023-2*17SF11 | Lower dual-row header partly in frame. |
| CN1 | 1 | 2×7 VIA port B header · A2547WV-N-2x7P | Dual-row header visible beside the VIA LEDs. |
| KBD | 1 | 1×4 keyboard header · RE-H042TD-1190 (LF)(SN) | Four pins visible at KBD. |

**Coverage:** 9 identified parts and 122 other design positions account for all 131 PCB references. IC sockets and removable shunts have no separate EasyEDA designators and are outside that count.

**Photo sources:** `PXL_20261123_204256348.MP.jpg` (upper board) and `PXL_20261123_204302996.MP.jpg` (lower board and overlapping components), 23 November 2026. Designators were matched against the [schematic](easyeda/6502-schematic.json) and [PCB layout](easyeda/PCB_V10.json).
