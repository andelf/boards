# Unbox

- 1 i5 v6.0 core board
- 1 adapter boards
- 2 x PMOD style pin female head

## ecpdap info

```concole
> ecpdap probes
Found 1 CMSIS-DAP probe:
  0d28:0204:07000001003500294700000d4e503054a5a5a5a597969908 DAPLink CMSIS-DAP


> ecpdap scan
Detected JTAG chain, closest to TDO first:
 - 0: 0x41111043 (Lattice Semi.) [IR length: 8]

(> ecpdap flash
ecpdap-flash 0.1.6
Access SPI flash attached to ECP5 when no other devices on JTAG chain

USAGE:
    ecpdap flash [FLAGS] [OPTIONS] <SUBCOMMAND>

FLAGS:
    -q, --quiet    Suppress informative output
    -h, --help     Prints help information

OPTIONS:
    -p, --probe <probe>                            VID:PID[:SN] of CMSIS-DAP device to use
    -f, --freq <freq>                              JTAG clock frequency to use, in kHz [default: 1000]
    -t, --tap <tap>                                ECP5's TAP position in scan chain (0-indexed, see `scan` output)
    -i, --ir-lengths <ir-lengths>...               Lengths of each IR, starting from TAP 0, comma-separated
    -l, --scan-chain-length <scan-chain-length>    Maximum JTAG scan chain length to check [default: 192]

SUBCOMMANDS:
    id           Read SPI flash ID
    scan         Read SPI flash parameters
    erase        Erase entire SPI flash
    write        Write binary file to SPI flash
    read         Read SPI flash contents to file
    protect      Set all block protection bits in status register
    unprotect    Clear all block protection bits in status register
    help         Prints this message or the help of the given subcommand(s)

> ecpdap flash id
Manufacturer 0xC8 (Apple Computer), Device 0x14/0x4015, Unique ID: 3142313533117F12
Finished in 0.24s

> ecpdap flash scan
Reading flash ID...
Manufacturer 0xC8 (Apple Computer), Device 0x14/0x4015, Unique ID: 3142313533117F12

Reading flash parameters...
SFDP JEDEC Basic Flash Parameter Table v1.0
  Density: 16777216 bits (2048 KiB)
  Address bytes: Three
  Legacy information:
    4kB erase supported: true
    4kB erase opcode: 0x20
    Block Protect always volatile: false
    Volatile write enable opcode: 0x50
    Writes have byte granularity: true
  Erase instructions:
    1: Opcode 0x20: 4096 bytes
    2: Opcode 0x52: 32768 bytes
    3: Opcode 0xD8: 65536 bytes
    4: Not present

Reading status registers...
Status 1: 0x1C, status 2: 0x00, status 3: 0x00
BP0: true, BP1: true, BP2: true, SEC: false, TB: false
Finished in 0.26s

> ecpdap flash read colorlight-i5.v6.0.bin
 Reading [========================================] 2.00MB/2.00MB (11.96KB/s; 00:00:00)
Finished in 171.89s
```

## unprotect spi flash

`BP0: false, BP1: false, BP2: false` in `flash scan` subcommand.

```console
> ecpdap flash unprotect
Disabling flash write protection...
Flash protected disabled.
Finished in 0.32s
```

```
> ecpdap flash scan
Reading flash ID...
Manufacturer 0xC8 (Apple Computer), Device 0x14/0x4015, Unique ID: 3142313533117F12

Reading flash parameters...
SFDP JEDEC Basic Flash Parameter Table v1.0
  Density: 16777216 bits (2048 KiB)
  Address bytes: Three
  Legacy information:
    4kB erase supported: true
    4kB erase opcode: 0x20
    Block Protect always volatile: false
    Volatile write enable opcode: 0x50
    Writes have byte granularity: true
  Erase instructions:
    1: Opcode 0x20: 4096 bytes
    2: Opcode 0x52: 32768 bytes
    3: Opcode 0xD8: 65536 bytes
    4: Not present

Reading status registers...
Status 1: 0x00, status 2: 0x00, status 3: 0x00
BP0: false, BP1: false, BP2: false, SEC: false, TB: false
Finished in 0.26s
```

## program flash

```
ecpdap flash write --freq 36000 blink.bit
 Erasing [========================================] 576.00KB/576.00KB (272.71KB/s; 00:00:00)
 Writing [========================================] 576.00KB/576.00KB (8.08KB/s; 00:00:00)
 Reading [========================================] 576.00KB/576.00KB (15.48KB/s; 00:00:00)
Finished in 111.37s
```

or use `ecpdap program` to load the `.bit` file to FPGA.

```
ecpdap program --freq 10000000 blink.bit
```

## Another board

```
ecpdap flash scan
Reading flash ID...
Manufacturer 0xC8 (Apple Computer), Device 0x14/0x4015, Unique ID: 314231353311430B

Reading flash parameters...
SFDP JEDEC Basic Flash Parameter Table v1.0
  Density: 16777216 bits (2048 KiB)
  Address bytes: Three
  Legacy information:
    4kB erase supported: true
    4kB erase opcode: 0x20
    Block Protect always volatile: false
    Volatile write enable opcode: 0x50
    Writes have byte granularity: true
  Erase instructions:
    1: Opcode 0x20: 4096 bytes
    2: Opcode 0x52: 32768 bytes
    3: Opcode 0xD8: 65536 bytes
    4: Not present

Reading status registers...
Status 1: 0x1C, status 2: 0x00, status 3: 0x00
BP0: true, BP1: true, BP2: true, SEC: false, TB: false
Finished in 0.37s
```

