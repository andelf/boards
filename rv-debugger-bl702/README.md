# Sipeed RV Debugger Plus / RV Debugger BL702

JTAG + UART Board.

Can be used as BL702(RV32) dev board.

## Chip & Board

BL702 32-bit RISC-V CPU.
(RV32, 144MHz, 132KB SRAM, 512KB Flash, Zigbee+BLE+USB)

- RV32IMAFBC RISC-V "SiFive E24 Core"

- BLE support (no antenna, but a 2.4G ipex pad)
- FTDI protocol?? = FT2232D
- 5V@500mA / 3V3@200mA

- frequency up to 144MHz
- 132KB RAM and 192 KB ROM
- 1Kb eFuse
- 512KB embedded Flash
- USB2.0 FS device interface

- LED0 for RX indication, LED1 for TX indication.

BL702C-A0.

- BL702: QFN32
- C: BLE/Zigbee
- A: 4Mbit inner flash = 512K
- 0: no pSRAM

### USB device

```
Bus 020 Device 016: ID 0403:6010 Future Technology Devices International Limited JTAG Debugger  Serial: FactoryAIOT Pro

JTAG Debugger:

    Product ID: 0x6010
    Vendor ID: 0x0403  (Future Technology Devices International Limited)
    Version: 5.00
    Serial Number: FactoryAIOT Pro
    Speed: Up to 12 Mb/s
    Manufacturer: SIPEED
    Location ID: 0x14100000 / 16
    Current Available (mA): 500
    Current Required (mA): 90
    Extra Operating Current (mA): 0
```

With `BOOT` pressed when powering up:

```
Bus 020 Device 017: ID ffff:ffff ffff CDC Virtual ComPort  Serial: 000000020000

CDC Virtual ComPort:

    Product ID: 0xffff
    Vendor ID: 0xffff
    Version: 2.00
    Serial Number: 000000020000
    Speed: Up to 12 Mb/s
    Manufacturer: BLIOT
    Location ID: 0x14100000 / 18
    Current Available (mA): 500
    Current Required (mA): 100
    Extra Operating Current (mA): 0

/dev/tty.usbmodem0000000200001
```

### Important Pins

15 GPIO pins.

| Pin     | Function  | Description |
| ------- | --------- | ----------- |
| GPIO_9  | LED0      | red         |
| GPIO_17 | LED1      | red         |
| GPIO_14 | UART0_TXD | 3pin hat    |
| GPIO_23 | UART0_RXD | 3pin hat    |

Other

| Pin     | Function  | Description |
| ------- | --------- | ----------- |
| GPIO_0  | JTAG_TDI  |             |
| GPIO_1  | JTAG_TDO  |             |
| GPIO_2  | JTAG_TMS  |             |
| GPIO_7  | USB_P     |             |
| GPIO_8  | USB_N     |             |
| GPIO_15 | JTAG_TCK  |             |
| GPIO_24 | UART1_RTS |             |
| GPIO_25 | UART1_CTS |             |
| GPIO_26 | UART1_TX  |             |
| GPIO_27 | UART1_RX  |             |
| GPIO_28 | UART1_DTR |             |

## Soft

Bouffalo Lab SDK

SiFive GCC Toolchain: https://www.sifive.com/software
riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-apple-darwin

Note: you might need to `xattr -cr path/to/toolchain`

### bflb-mcu-tool

国人写工具的特点, BUG 多多.
不开源, pip 的也是旧的，虽可以看到源码. 但该工具的初衷是在 sdk 源码目录 `tools` 下执行。

```
pip install pylink
pip install portalocker
pip install bflb-mcu-tool
```

需要修改修正 import

```py
sys.path.insert(0, os.path.dirname(os.path.dirname(__file__)))
```

```
cd RV-Debugger-BL702/tools/bflb_flash_tool
cp -r chips/ py3/lib/python3.9/site-packages/bflb_mcu_tool/
```

折腾失败. 放弃.

### Board SDK + Bouffalo SDK

https://github.com/sipeed/RV-Debugger-BL702
https://gitee.com/bouffalolab/bl_mcu_sdk

```sh
cp -v ../RV-Debugger-BL702/bsp/board/bl702_debugger ./bsp/board/

export PATH=$HOME/opt/riscv64-unknown-elf-toolchain-10.2.0/bin:$PATH

# the underlying cmd uses cmake
rm -r build
# (I changed pins in gpio_blink, to use the debugger leds)
make APP=gpio_blink BOARD=bl702_debugger

ls -lah out/examples/gpio/gpio_blink/gpio_blink_main.bin

ls -lah tools/bflb_flash_tool/img/project.bin

# flashing
bflb-mcu-tool --chipname=bl702 --interface=uart --xtal=32M --port=/dev/tty.usbmodem0000000200001 --baudrate=2000000

# or simply
bflb_mcu_tool --chipname bl702
```

### Error fix

```
[ 98%] Linking C executable ../../../../out/examples/gpio/gpio_blink/gpio_blink_main.elf
../riscv64-unknown-elf-toolchain-10.2.0/bin/../lib/gcc/riscv64-unknown-elf/10.2.0/../../../../riscv64-unknown-elf/bin/ld: warning: cannot find entry symbol _enter; defaulting to 0000000023000000
```

```
Requires linker script in CMakeLists.txt

set(LINKER_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/usb_flash.ld)
```

### Bouffalo Lab Dev Cube

https://dev.bouffalolab.com/download

The macOS version is junk. Crashes.

## Antenna for BLE?

Missing C19, C22, ANT1.

20x12mm 陶瓷贴片天线焊盘.

需要:

- 蓝牙贴片陶瓷天线, 每个 20x12mm 大小, 可以使用 2051 天线, 型号 AN2051-245.

## Notes

macOS 下 USB 转 UART 可能支持不了 2M UART 速率!!!

## Links

- https://github.com/sipeed/RV-Debugger-BL702
- SDK Guide: http://bouffalolab.gitee.io/bl_mcu_sdk/
- https://gitee.com/bouffalolab/bl_mcu_sdk
- https://github.com/bouffalolab/bl_mcu_sdk

## Other Refs

flash tool in rust for BL602
https://github.com/spacemeowx2/blflash

Flashing Firmware to PineCone BL602
https://lupyuen.github.io/articles/flash

https://github.com/bouffalolab/BLOpenFlasher

https://github.com/bouffalolab/flash_tools
