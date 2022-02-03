# Basys 3 Artix-7 FPGA Trainer Board

Xilinx Artix-7 FPGA

- Onboard XC7A35T-1CPG236C Xilinx Artix-7 FPGA
- 16 user switches, 16 user LEDs and 5 user pushbuttons
- 4 digit 7 segment display
- 3 standard 12 pin Pmod and 1 dual purpose XADC signal/standard Pmod connector
- 12bit VGA output
- FTDI FT2232HQ USB-UART bridge
- 32Mbit non volatile serial flash
- Digilent USB-JTAG port for FPGA programming and communication
- USB HID host for mice, keyboards and memory sticks

Artix-7 35T
- 33,280 logic cells in 5200 slices (each slice contains four 6-input LUTs and 8 flip-flops)
- 1,800 Kbits of fast block RAM
- Five clock management tiles, each with a phase-locked loop (PLL)
- 90 DSP slices
- Internal clock speeds exceeding 450MHz
- On-chip analog-to-digital converter (XADC)

FT2232 for UART + JTAG

## Soft

programmer: Adept 2, downloaded from digilent.

### Xilinx Vivado

Xilinx's Vivado Design Suite

!! 2020.3 only supports Versal devices!

Routine:
- Install Vivado, about 20G download
- Add board files to Vivado install dir
- Create project, RTL project
- add xdc file of the board
- set Quad SPI flash(Spansion, 32bit)

Install Guide:
https://reference.digilentinc.com/vivado/installing-vivado/v2019.2

Board Files:
https://github.com/Digilent/vivado-boards/archive/master.zip

'C:/Xilinx/Vivado' or '/opt/Xilinx/Vivado' by default. Under this folder, navigate to its '<version>/data/boards/board_files'

Help => "Add Design Tools or Devices"

XDC files:
https://github.com/Digilent/digilent-xdc/archive/master.zip
(Copy to project)

Programming Setting(spi flash):
https://reference.digilentinc.com/learn/programmable-logic/tutorials/basys-3-programming-guide/start

## Links

- Store: https://store.digilentinc.com/basys-3-artix-7-fpga-beginner-board-recommended-for-introductory-users/
- Datasheet PDF: http://www.farnell.com/datasheets/1884379.pdf
- Ref: https://reference.digilentinc.com/programmable-logic/basys-3/start?redirect=1
- https://reference.digilentinc.com/programmable-logic/basys-3/reference-manual?redirect=1
