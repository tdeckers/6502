# Yet another 6502 project

## Overview

This started as virtually all other 6502 projects: with Ben Eaters' excellent Youtube videos.  It initially got me inspired to get a 6502 working.  And then I kept on adding components and features.  To get rid of the fragile mess on my breadboard I decided to create a PCB.  Although this repository mainly serves as a reference for myself as I continue to build onto the project, I hope it can help others with their own 6502 adventures.

## Features

*Works:*
* keyboard I/O - see keyboard controller
* VGA - see VIC II FPGA project
* C64 OS
* "game of life"

*Doesn't work:*
* everything else
* specifically: other video modes

## Building blocks

PCB and main components
GAL, logic and programming.
Keyboard controller: https://github.com/tdeckers/ps2kbd-controller
VGA board (partial VIC II in FPGA): https://github.com/tdeckers/fpga-vicii
Serial interface (for loading programs)

## Tools

TL866 II Plus: ROM and GAL programming
Pickit4 (upgraded from pickit3): PIC keyboard controller

## Troubleshooting

Arduino Mega - see Ben's video.
Key concepts:
* take control of the clock, run slower or step through.
* monitor address, data bus.

