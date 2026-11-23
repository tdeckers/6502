# 6502 board · PCB_V10

Captured from the private 6502 project on 2026-11-23. Open either JSON file in EasyEDA Standard with **File → Open → EasyEDA**. [Original project](https://easyeda.com/editor#id=615a0c21201b49a898c2804ee4c0b9e0%7C0339cd2a47ae4208b21eaa0255a6446d) · [EasyEDA source-file guide](https://docs.easyeda.com/en/Export/Export-EasyEDA-Source-File/index.html)

| Editable source | Contents |
| --- | --- |
| [6502-schematic.json](6502-schematic.json) | All four sheets and their symbols: 6502 and 6522, Keyboard, Power, Interfaces |
| [PCB_V10.json](PCB_V10.json) | Board outline, footprints, pad nets, routing, copper areas and design rules |

The two files contain the same 131 component designators. EasyEDA's PCB design manager showed 121/121 nets and zero DRC errors. The review below uses the saved pad connections and the [GAL equations](../../gal/decoder.pld). The [populated-board BOM](../BOM.md) identifies the fitted ICs and 1 MHz oscillator. The programmed GAL contents and physical signals still need bench verification.

## Review of the populated board at 1 MHz

With H5 linking X2 to CLK, the nominal CPU cycle is 1,000 ns. At 5 V and an assumed 50% duty cycle, both fitted memories have ample **read** time on paper. The first wiring fix is to qualify U5 output enable. Slowing the clock cannot prevent bus contention on a write to ROM. The clock at U1 pin 37 and the waveforms at the memories have yet to be measured.

| Read path | Available time | Worst-case path through the fitted parts | Margin at 1 MHz |
| --- | ---: | --- | ---: |
| U5 EEPROM, address-decoded /CE | 1,000 ns, falling edge to falling edge | 30 ns CPU address delay + 25 ns GAL decode + 150 ns EEPROM access from /CE + 10 ns CPU setup | 785 ns |
| U6 SRAM, PHI2-qualified /CE | 500 ns, rising edge to falling edge | 25 ns GAL decode + 55 ns SRAM access from /CE + 10 ns CPU setup | 410 ns |

These margins cover the current read wiring and exclude board loading and clock skew. Writes need a separate hold-time check. If RWB stays low on consecutive RAM writes, U3 can release /CE up to 25 ns after PHI2 falls, while U1 guarantees the old address and write data for only 10 ns. Check the end of the write at U6 even at 1 MHz. [CPU timing, Table 6-3](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf) · [GAL22V10D timing](https://www.latticesemi.com/-/media/LatticeSemi/Documents/DataSheets/GAL/GAL22V10DataSheet.ashx) · [AT28C256 timing](https://ww1.microchip.com/downloads/aemDocuments/documents/MPD/ProductDocuments/DataSheets/AT28C256-Industrial-Grade-256-Kbit-Paged-Parallel-EEPROM-Data-Sheet-DS20006386.pdf) · [AS6C62256 timing](https://www.alliancememory.com/wp-content/uploads/AS6C62256-23-March-2016-rev1.2.pdf)

| Circuit | Finding and follow-up |
| --- | --- |
| EEPROM bus contention | U5 pin 22 (/OE) is grounded and pin 27 (/WE) is at +5 V. U3 pin 20 selects the ROM solely by address. A CPU write to a ROM address such as $E000 enables both the EEPROM and CPU data outputs. Qualify ROM output enable with the read direction so U5 releases D0–D7 during writes. [AT28C256 operating modes](https://onlinedocs.microchip.com/oxy/GUID-7B73D324-9159-4988-92D8-6062218CD25C-en-US-2/GUID-D6DBCF7C-05FF-418D-8F92-F6EC72BB55D4.html) |
| CPU interrupt input | **Bad at both 1 MHz and 8 MHz.** U8 pin 6 drives the active-low IRQB input at U1 pin 4. With IRQ idle, the fitted XD74LS00 guarantees only 2.7 V high at VCC = 4.75 V and 0.4 mA source current. On that same supply the W65C02S requires at least 4.35 V (VDD − 0.4 V), a **1.65 V shortfall**. A board may appear to work, but the CPU can see an interrupt when both peripherals have released it. [Xinluda XD74LS00, §7](https://www.xinluda.com/upload/datasheet/XD74LS00.pdf) · [W65C02S DC characteristics](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf) |
| FPGA input levels | All 29 H7 signal dividers have 3.3 kΩ in series and 2.2 kΩ to ground, including CLK (R67/R68). An ideal 5 V high becomes 2.00 V. The Cyclone 10 LP's 3.3 V LVTTL minimum high is 1.7 V, so a source below 4.25 V misses the spec even before edge settling. Check CLK and a GAL chip-select at H7 with the FPGA attached. A 3.3 V buffer with 5 V-tolerant inputs would give a defined interface. [Altera I/O limits](https://docs.altera.com/r/docs/683251/current/cyclone-10-lp-device-datasheet/single-ended-i/o-standard-specifications) |
| Reset and overflow | R1 pulls RESB high and KEY1 pulls it low manually. There is no power-on reset delay. WDC requires at least two clocks of reset after the supply reaches operating voltage. U1 pin 38 (SOB) also has no net. Pull it high to prevent noise from setting the overflow flag. [W65C02S reset and SOB](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf) |
| SRAM aliases | U6 has A0–A14 only. The GAL also selects it at $8000–$9FFF and $D800–$DBFF. Those writes overwrite $0000–$1FFF and $5800–$5BFF respectively. For example, a write to $8000 changes zero-page address $0000. Reserve the aliases in software or revise the memory map if independent storage is required. [Decoder source](../../gal/decoder.pld) |
| Keyboard pull-ups | KBD pins 2 and 3 run directly to U9 pins 18 and 17. The board provides no pull-up on either open-collector PS/2 signal. Check whether the keyboard or adapter supplies them. A different keyboard or cable can expose the missing bias. [PS/2 electrical interface](https://digilent.com/reference/_media/pmod%3Apmod%3ApmodPS2_rm.pdf) |

### IRQ indicator load and fix

When IRQ is asserted, U8 sinks current from +5 V through R10 and LED5. Xinluda specifies a maximum low output of 0.4 V at 4 mA and 0.5 V at 8 mA. The CPU accepts at most 0.4 V. That leaves zero guaranteed low-level margin at 4 mA and misses the limit at 8 mA. The fitted R10 value is unresolved. Even a lightly loaded LED leaves the idle-high mismatch above. [Xinluda electrical characteristics](https://www.xinluda.com/upload/datasheet/XD74LS00.pdf) · [WDC input levels](https://www.wdc65xx.com/wdc/documentation/w65c02s.pdf)

A practical repair is the pin-compatible SN74HCT00N at U8. It accepts TTL inputs and provides a CMOS high for the lightly loaded CPU input. Use 2.2 kΩ at R10 to keep the LED branch below 2.4 mA even at 5.25 V, or drive LED5 separately. Verify both states at U1 pin 4 with the indicator connected. At a measured 5.0 V supply, look for at least 4.6 V idle and at most 0.4 V asserted. [TI SN74HCT00N, pinout and electrical characteristics](https://www.ti.com/lit/ds/symlink/sn74hct00.pdf)

### Assembly discrepancy

R6–R10 and R59 read **220 Ω** on the Power sheet and **1 kΩ** in the PCB. Their schematic purchasing fields also specify the 1 kΩ part (RS-05K1001FT, C115316). They limit the chip-select, IRQ and power LED currents. Choose the intended load and synchronize the six values before ordering an assembly.

## Switching to an 8 MHz oscillator

At 8 MHz, PHI2 has a 125 ns period and nominal 62.5 ns high and low phases. With the same fitted parts and equations, the EEPROM read path needs 215 ns and the SRAM read path needs 90 ns of clock high. **Both paths need a design change before a straight oscillator swap can meet worst-case timing.**

| Circuit | Change for 8 MHz |
| --- | --- |
| **U5 EEPROM reads** | Keep the 150 ns EEPROM and add a decoded ROM-read wait state, or fit faster, pin-compatible memory. One extra full bus cycle gives 250 ns total for the current 215 ns path. For zero waits, the present decoder leaves at most **60 ns from /CE to data** (125 − 30 − 25 − 10), before guard band. Also budget address access and the new read-qualified /OE path. H9 exposes RDY with a pull-up. A wait-state design needs logic that asserts and releases it in time. |
| **U6 RAM reads and writes** | One way to keep the 55 ns SRAM at full speed is to decode /CE from address during PHI2 low, qualify /OE for reads, and generate /WE only during a selected PHI2-high write. This requires changing the GAL equations and control wiring. U6 pin 22 is grounded, pin 27 takes raw RWB, and U3 currently has no RWB input. Budget the write strobe at the SRAM pins against its 45 ns minimum /WE pulse, 50 ns /CE-to-write-end and 25 ns data setup. End the strobe within the CPU's address/data hold window. An alternative is to stretch selected RAM cycles with a controller that also guarantees write termination. [SRAM timing](https://www.alliancememory.com/wp-content/uploads/AS6C62256-23-March-2016-rev1.2.pdf) |
| **U2 VIA select** | The fitted W65C22S-14 is fast enough at 5 V, but its select must settle before PHI2 rises. Allowing 30 ns for the CPU address, 25 ns for U3 and 10 ns for VIA select setup requires **65 ns** within a nominal **62.5 ns** low phase. Use a faster decoder or lengthen the low phase for VIA accesses. Check against the oscillator's shortest specified low phase and include skew. [VIA timing, Table 4-3b](https://www.wdc65xx.com/wdc/documentation/w65c22.pdf) |
| **U4 ACIA access** | U4 pin 27 shares the CPU clock. WDC marks the fitted part -14, but its published AC table gives bus timing only for 2 and 4 MHz. Obtain applicable 8 MHz timing or design a synchronized slower peripheral bus cycle with compliant PHI2 pulses and select timing. A CPU RDY wait alone still clocks U4 at 8 MHz. X1 supplies the separate serial baud reference. [W65C51N timing, Table 4 and ordering information](https://wdc65xx.com/wdc/documentation/w65c51n.pdf) |

**Clock, firmware and bench checks.** Fit a 5 V, pin-compatible 8 MHz oscillator at X2 and verify its enable-pin behavior, output levels, duty cycle and edge rates under load. At H7, check clock, data and select setup at the FPGA with the cable attached. The dividers already have limited high-level margin at 1 MHz. Use a suitable level translator if the loaded interface misses voltage or timing limits. Recalculate VIA timer reloads and CPU delay loops used for real-time intervals.

For bring-up, capture PHI2 at U1 pin 37 alongside /CE and data on ROM reads, then /WE, address and data on back-to-back RAM writes. Exercise reset-vector fetch, RAM readback, VIA interrupts, serial transmit/receive and FPGA video writes. Compare the measured edges with the budgets above, including the oscillator's duty-cycle tolerance.
