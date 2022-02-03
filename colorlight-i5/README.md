# Colorlight i5 - v6.0

- FPGA: Lattice LFE5U-25F-6BG381C
- SDRAM: EM638325BK-6H 8MB
  (bank_sel[1] is always tied to GND, so just 4MB we can use.)
- SPI FLASH: GD25Q16CSIG 2MB
- Ethernet PHY: 1Gb Ethernet PHY Broadcom B50612D x 2

- 24K LUTs
- 1008Kb Embedded Memory
  - 56 x sysMEM block RAMs (of 18Kb each), good for up to 126KByte of on-chip RAM
- 194Kb of distributed RAM
- 28x DSPs with 18x18 multiplier and 36-bit accumulator
- 2 PLLs, 2 DLLs
- 0 SERDES
- 381 caBGA package
- 197 I/O

The LFE5U-25 version is one of the smaller versions in the ECP5 family.

Note: (iCESugar-pro is LFE5U-25F-6BG256C)

## Board

- On-Board Debugger DAPLink with JTAG & USB CDC
- 6 x Dual-PMOD, 100+ usable IOs
  - 6 x (2 x 15)
  - P1 is for ETH1, ETH2
- TYPE-C USB
- HDMI (be careful when soldering nearby sockets, it'll block the HDMI socket, or leave P2/P3 empty)

U16 led_green  PR47C = P2_28

![Pin Assignment](https://github.com/wuxx/Colorlight-FPGA-Projects/raw/master/doc/i5_extboard_v1.2_pinout.png)

## Soft

### ecpdap - program

ECPDAP allows you to program ECP5 FPGAs and attached SPI flash using CMSIS-DAP probes in JTAG mode.

https://github.com/adamgreig/ecpdap/

cargo install ecpdap

```console
> ecpdap probes
Found 1 CMSIS-DAP probe:
  0d28:0204:070000010669ff343832434257103422a5a5a5a597969908 DAPLink CMSIS-DAP

> ecpdap scan

# load to FPGA
> ecpdap program --freq 9000 blink.bit

# program to FLASH
> ecpdap flash write --freq 5000 blink.bit
```

ecpdap flash unprotect

### old routine

```
# *.v => *.json
yosys -p "synth_ecp5 -json blink.json" blink.v rst_gen.v

# *.json *.lpf => *.config
~/opt/nextpnr/bin/nextpnr-ecp5 --25k --package CABGA381 --speed 6 --json blink.json --freq 65 --lpf blink.lpf --textcfg blink_out.config

# *.config => *.bit *.svf
~/opt/nextpnr/bin/ecppack blink_out.config blink.bit --svf blink.svf

jtag.sh blink.svf
```

### icesprog

https://github.com/wuxx/icesugar/blob/master/tools/src/icesprog.c
https://github.com/FPGAwars/toolchain-icesprog

hid protocol.

    clang icesprog.c `pkg-config --cflags --libs libusb-1.0` `pkg-config --cflags --libs hidapi`

## Note

- Unprotect(unlock) spi flash before use.


## Links

- https://github.com/wuxx/Colorlight-FPGA-Projects
- MUST read: https://tomverbeure.github.io/2021/01/22/The-Colorlight-i5-as-FPGA-development-board.html
- https://github.com/adamgreig/ecpdap/
- http://www.clifford.at/icestorm/

### Projects?

FPGA 8-Bit TV80 SoC for Lattice iCE40 with complete open-source toolchain flow using yosys and SDCC
https://github.com/abnoname/iceZ0mb1e

https://github.com/emeb/up5k_6502
A simple 6502 system built on a Lattice Ultra Plus 5k FPGA

