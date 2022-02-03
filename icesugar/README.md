# iCESugar

iCE40UP5k FPGA 开发板.

Lattice UltraPlus Family.

## Chip & Board

iCE40UP5K-SG48

```
5280 Logic Cells (4-LUT + Carry + FF)
128 KBit Dual-Port Block RAM
1 MBit (128 KB) Single-Port RAM
PLL, Two SPI and two I2C hard IPs
Two internal oscillators (10 kHz and 48 MHz)
8 DSPs (16x16 multiply + 32 bit accumulate)
3x 24mA drive and 3x hard PWM IP
```

```
iCE40-UP5K-SG48
48-pin QFN (7 x 7 mm)
pin spacing: 0.50 mm
I/Os: 39
nextpnr opts: --up5k --package sg48
arachne-pnr opts: -d 5k -P sg48
icetime opts: -d up5k
```

```
EBR RAM (kbits): 120
SPRAM (kbits): 1024
PLL: 1
I2C Core: 2
SPI Core: 2
Oscillator (10 kHz): 1
Oscillator (48 MHz): 1
24 mA Drive: 3
PWM: y
```

- ref: https://www.latticesemi.com/en/Products/FPGAandCPLD/iCE40UltraPlus

- 3 PMOD
- 1 JTAG
- RGB Led, +3 IO
- 4 toggle switch, +4 IO
- 1 UART (TX/RX, 2 of PMOD1)
- 6 pin between PMOD2 and PMOD3, +4 IO
- 1 USB 2.0 micro
- 1 USB 3.0 type-C for


### USB device

lsusb

```
Bus 020 Device 019: ID 1d50:602b 1d50 DAPLink CMSIS-DAP  Serial: 07000001002600574700.......
```

```
# iCELink Firmware by MuseLab - see muselab-tech.com
Build Time: Mar 15 2020 13:35:57
Unique ID: 0700000100260057470000.......
HIC ID: 97969908
Auto Reset: 1
Automation allowed: 1
Overflow detection: 1
Daplink Mode: Interface
Interface Version: 0254
Bootloader Version: 0254
Git SHA: 79e9a58ac40c44a173626e290cd9651a39abcd0b
Local Mods: 1
USB Interfaces: MSD, CDC, HID, WebUSB
Bootloader CRC: 0x768fc5e4
Interface CRC: 0x82203d9b
Remount count: 0
URL: @R
```

## Soft

FPGA综合（yosys），布线（arachne-pnr & nextpnr）, 打包烧录（icestorm），编译（gcc）

NextPNR (place & route tool, Arachne-PNR replacement).

```sh
# icestorm, 脚本里用了 gnu sed 特有参数, bsd sed 无法支持
brew install gnu-sed
export PATH="/usr/local/opt/gnu-sed/libexec/gnubin:$PATH"

make -j8 install DESTDIR= PREFIX=$HOME/opt/fpga

# nextpnr
cmake .. -DARCH=ice40 -DCMAKE_INSTALL_PREFIX=$HOME/opt/fpga -DICESTORM_INSTALL_PREFIX=$HOME/opt/fpga
make -j8 install

# also with ecp5 support?
# (install prjtrellis/libtrellis)
cd libtrellis # must run from this dir
cmake . -DCMAKE_INSTALL_PREFIX=$HOME/opt/fpga

cmake .. -DCMAKE_INSTALL_PREFIX=$HOME/opt/fpga -DTRELLIS_INSTALL_PREFIX=$HOME/opt/fpga/ -DICESTORM_INSTALL_PREFIX=$HOME/opt/fpga  -DBUILD_GUI=OFF -DARCH="ice40;ecp5"
make -j8 install
```

### nextpnr

```
-DICESTORM_INSTALL_PREFIX=/usr
```

### icestorm

```
icebox_explain xxx.asc

# Show Only tile 1 0
icebox_explain -mAt '1 0' initializer.asc

icebox_stat $(PROJECT).asc

icebox_vlog initializer.asc

icetime -c 60 -d up5k seg_disp.asc
```

### icesprog

https://github.com/wuxx/icesugar/blob/master/tools/src/icesprog.c
https://github.com/FPGAwars/toolchain-icesprog

hid protocol.

    clang icesprog.c `pkg-config --cflags --libs libusb-1.0` `pkg-config --cflags --libs hidapi`

Same as Colorlight i5 board.

```sh
-p probe

icesprog -e
```




## Notes

The input frequency into the prescaler in the iCESugar is 12MHz.
From STM32 MCO1(PA8) pin.

### technology library

http://www.latticesemi.com/~/media/LatticeSemi/Documents/TechnicalBriefs/SBTICETechnologyLibrary201504.pdf

IO primitive setting:

```verilog
SB_IO #(
      .PIN_TYPE(6'b1010_01),
      .PULLUP(pu)
  ) io_pin (
      .PACKAGE_PIN(pin),
      .OUTPUT_ENABLE(oe),
      .D_OUT_0(din),
      .D_IN_0(dout)
  );
```

### internal oscillators

The ice40 ultraplus has two internal clocks, 48MHz and 10KHz.

HF Oscillator
LF Oscillator

To Calc PLL:

```
icepll -i 48 -o 24
```

较高频率可能无法得到满足。

ref: iCE40 Oscillator Usage Guide

### SPRAM

Single Port RAM?

SB_SPRAM256KA


### BRAM

The ice40 ultraplus has 30 BRAM of 4kbit.

EBR = Embedded Block RAM

128 KBit


## 存在问题

丝印标注在了背面，查看不够方便。
对 PMOD 针脚的数字标号和官方 PMOD 标准不一致。
P4, P6 共用 PMOD1 和 UART, 需要用跳线帽选择, 容易被误导。

使用过程中坏掉了, 上电进入 maintenance 模式，无法烧写。
发现是 RST 无法被上拉导致。直接短接 R1 再启动，进入正常模式。
但无法修复，每次上电都需要如此操作。

## Links

- https://github.com/wuxx/icesugar
- Bilibili Intro Video: https://www.bilibili.com/video/BV1n7411d7ys
- http://bygone.clairexen.net/icestorm/
- https://www.latticesemi.com/en/Products/FPGAandCPLD/iCE40UltraPlus
