# UP Board

- Intel® Atom™ x5-z8350
  - Intel Cherry Trail Z8350
  - Quad Core
  - Intel® HD 400 Graphics
  - 480MHz to 1.44GHz, Turbo up to 1.92GHz
- Intel® FPGA Altera Max V for 40-pin GP-bus
  - 40-pin GPIO expansion
- 4GB RAM
- 32GB Storage
- UEFI BIOS

- 4 x USB2.0 port Type A
- 2 x USB2.0 pin header
- 1 x USB 3.0 OTG Micro B
- 2 x UART (Tx/Rx) debug port (pin header)
- 1x MIPI-CSI 2 lane
- 40-pin GP-bus
- 1 x GbLAN (Realtek RTL8111G-CG)
- 1 x (HDMI 1.4a)
- MIPI DSI/eDP
- I2S audio port

- 3 LEDs (yellow, green, red) on the underside of the board (underneath the 4 USB2.0 Type-A sockets)

## BIOS Upgrade/Settings

default version UPC1DM11

current version UPC1DM23 https://downloads.up-community.org/

https://github.com/up-board/up-community/wiki/Firmware#opensource-uefi-bios

## Kernel build

https://github.com/hdezela/linux

https://patchwork.kernel.org/project/linux-kbuild/patch/2d0bd4a4-98cb-380e-36c4-e46dc5d53991@molgen.mpg.de/

```
pager.c: In function ‘pager_preexec’:
pager.c:35:19: error: passing argument 2 to ‘restrict’-qualified parameter aliases with argument 4 [-Werror=restrict]
   35 |         select(1, &in, NULL, &in, NULL);
```

HOST_CFLAGS

-Wno-error=restrict

rm -Werror

-Wrestrict is default in -Wall starting from GCC 8.0

### 4.4.236

```
$ uname -a
Linux up-board 4.14.236-upboard #1 SMP PREEMPT Wed Jun 16 17:58:59 CST 2021 x86_64 GNU/Linux
```

## On-board Devices

### UART

- 10-pin socket, UART0, /dev/ttyS0
- 40-pin slot, UART1, /dev/ttyS1
- USB 3.0 Micro B OTG, UART, virtual

Chipset > South Bridge > Default DRD Config: DeviceMode
Chipset > South Bridge > USB OTG Support: PCI mode

加载USB Gadget驱动：

sudo modprobe g_serial
sudo systemctl start getty@ttyGS0

### 40-pin GPIO

- 2 x PWM
-


### Slots

USB 2.0 x2 Slot

From right to left:

- 5V(red)
- D-(white)
- D+(green)
- GND(black)
- 5V(red)
- D-(white)
- D+(green)
- GND(black)
- UART?
- UART?

50mm x 30mm
55mm x 35mm

### Leds



## CPU

### MWAIT

https://github.com/up-board/up-community/wiki/Enable_intel_idle

## Links

- https://up-board.org/up/specifications/

- https://www.cnblogs.com/sjqlwy/p/up_review.html
- https://www.cnblogs.com/sjqlwy/p/up_serial.html
