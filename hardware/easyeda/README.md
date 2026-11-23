# 6502 board · PCB_V10

Captured from the private 6502 project on 2026-11-23. Open either JSON file in EasyEDA Standard with **File → Open → EasyEDA**. [Original project](https://easyeda.com/editor#id=615a0c21201b49a898c2804ee4c0b9e0%7C0339cd2a47ae4208b21eaa0255a6446d) · [EasyEDA source-file guide](https://docs.easyeda.com/en/Export/Export-EasyEDA-Source-File/index.html)

| Editable source | Contents |
| --- | --- |
| [6502-schematic.json](6502-schematic.json) | All four sheets and their symbols: 6502 and 6522, Keyboard, Power, Interfaces |
| [PCB_V10.json](PCB_V10.json) | Board outline, footprints, pad nets, routing, copper areas and design rules |

The two files contain the same 131 component designators. EasyEDA's PCB design manager showed 121/121 nets and zero DRC errors. The review below uses the saved pad connections and the [GAL equations](../../gal/decoder.pld). Fitted parts, programmed GAL contents, clock speed and physical signals still need bench verification.

## Electrical findings

| Circuit | Finding and follow-up |
| --- | --- |
| EEPROM bus contention | U5 pin 22 (/OE) is grounded and pin 27 (/WE) is at +5 V. U3 pin 20 selects the ROM solely by address. A CPU write to a ROM address such as $E000 enables both the EEPROM and CPU data outputs. Qualify ROM output enable with the read direction so U5 releases D0–D7 during writes. [AT28C256 operating modes](https://onlinedocs.microchip.com/oxy/GUID-7B73D324-9159-4988-92D8-6062218CD25C-en-US-2/GUID-D6DBCF7C-05FF-418D-8F92-F6EC72BB55D4.html) |
| 8 MHz memory timing | With X2 jumpered through H5, a cycle is 125 ns. A standard AT28C256-15 allows 150 ns for a read before adding decode delay or CPU setup. U6 has a separate limit. Its /CE comes through the GAL only during clock high. At 50% duty, the 62.5 ns window is already shorter than 55 ns SRAM access plus 10 ns W65C02S setup. Check U5's actual suffix and the clock at U1 pin 37. Use a slower clock for bring-up. [EEPROM timing](https://ww1.microchip.com/downloads/aemDocuments/documents/MPD/ProductDocuments/DataSheets/AT28C256-Industrial-Grade-256-Kbit-Paged-Parallel-EEPROM-Data-Sheet-DS20006386.pdf) · [SRAM timing](https://www.alliancememory.com/wp-content/uploads/AS6C62256-23-March-2016-rev1.2.pdf) · [CPU timing, Table 6-3](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf) |
| CPU interrupt input | U8 pin 6 directly drives U1 pin 4 (IRQB). TI guarantees 2.5 V high for the SN74LS00 at its specified load. The W65C02S specifies VDD − 0.4 V for IRQB, or 4.6 V at 5 V. Use a gate with compatible input and output levels and account for the LED5 load. [TI electrical characteristics](https://www.ti.com/lit/ds/symlink/sn74ls00.pdf) · [WDC DC characteristics](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf) |
| FPGA input levels | All 29 H7 signal dividers have 3.3 kΩ in series and 2.2 kΩ to ground, including CLK (R67/R68). An ideal 5 V high becomes 2.00 V. The Cyclone 10 LP's 3.3 V LVTTL minimum high is 1.7 V, so a source below 4.25 V misses the spec even before edge settling. Check CLK and a GAL chip-select at H7 with the FPGA attached. A 3.3 V buffer with 5 V-tolerant inputs would give a defined interface. [Altera I/O limits](https://docs.altera.com/r/docs/683251/current/cyclone-10-lp-device-datasheet/single-ended-i/o-standard-specifications) |
| Reset and overflow | R1 pulls RESB high and KEY1 pulls it low manually. There is no power-on reset delay. WDC requires at least two clocks of reset after the supply reaches operating voltage. U1 pin 38 (SOB) also has no net. Pull it high to prevent noise from setting the overflow flag. [W65C02S reset and SOB](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf) |
| SRAM aliases | U6 has A0–A14 only. The GAL also selects it at $8000–$9FFF and $D800–$DBFF. Those writes overwrite $0000–$1FFF and $5800–$5BFF respectively. For example, a write to $8000 changes zero-page address $0000. Reserve the aliases in software or revise the memory map if independent storage is required. [Decoder source](../../gal/decoder.pld) |
| Keyboard pull-ups | KBD pins 2 and 3 run directly to U9 pins 18 and 17. The board provides no pull-up on either open-collector PS/2 signal. Check whether the keyboard or adapter supplies them. A different keyboard or cable can expose the missing bias. [PS/2 electrical interface](https://digilent.com/reference/_media/pmod%3Apmod%3ApmodPS2_rm.pdf) |

## Assembly discrepancy

R6–R10 and R59 read **220 Ω** on the Power sheet and **1 kΩ** in the PCB. Their schematic purchasing fields also specify the 1 kΩ part (RS-05K1001FT, C115316). They limit the chip-select, IRQ and power LED currents. Choose the intended load and synchronize the six values before ordering an assembly.
