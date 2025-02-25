<img src="https://craterdog.com/images/CraterDog.png" width="50%">

## Go Simple Processor Simulator

### Overview
This project defines the design and instruction set for a simple 16 bit parallel
processor whose purpose is to handle the processing of one request from an
external source and resulting in one response back to the external source.

![Processor Flow](https://raw.githubusercontent.com/wiki/craterdog/go-simple-processor/docs/images/Processor%20Flow.jpg)

It also provides a Go based simulator for the processor.

### Architecture
This simple processor architecture is made up of two major components—the
Central Processing Unit (CPU) and the Memory Unit.

![Architecture Model](https://raw.githubusercontent.com/wiki/craterdog/go-simple-processor/docs/images/Processor%20Architecture.jpg)

#### Registers
 * `Rn` {general purpose registers: `R1` - `R4`}
 * `PC` {program counter containing the address of the current instruction}
 * `IR` {instruction register containing the instruction being executed}
 * `AD` {address register containing the end of the address space}
 * `ST` {status register containing nine system flags and seven user flags}

#### Flags
 * `Fn` {general purpose user flags: `F1` - `F7` which are setable}
 * `FC` {carry flag which is setable}
 * `FO` {overflow flag}
 * `FN` {negative result flag}
 * `FZ` {zero result flag}
 * `FB` {boot mode flag}
 * `FD` {data mode flag}
 * `FI` {I/O error flag}
 * `FA` {address error flag}
 * `FP` {permission error flag}

#### Memory
 * 64K of addressable words {`0x0000` - `0xFFFF`}
 * 1 word = 16 bits
 * Address `0x0000` is for I/O {readable and writable}
 * Addresses `0x0001` - `0x0007` are the persistent, read-only boot instructions
 * Addresses `0x0008` - `0xFFFF` are random access memory (RAM)

### Instruction Set
The instruction set for this processor consists of 45 instructions and one
pseudo instruction (`SKIP`) which is equivalent to the `JUMP BY 1` instruction
(the traditional "NOP" instruction).

Except during the boot process (i.e. `FB` is `TRUE`) the `Rz` register cannot be
any of the system registers to ensure the integrity of the processor.  The `Rx`
and `Ry` registers may be any of the eight registers.  Similarly, only the user
status flags may be set or cleared, again to ensure the integrity of the
processor.

| Mnemonic                    | Instruction        | Description
|-----------------------------|--------------------|--------------------------------|
| **Control Operations** | | |
| `SKIP`                      | `0000000000000000` | Forward by `1`.                |
| `JUMP BY offset`            | `0000oooooooooooo` | Forward by `[1..4096]`.        |
| `JUMP BY -offset`           | `0001oooooooooooo` | Backward by `[1..4096]`.       |
| `JUMP BY offset ON Fn`      | `0010nnnooooooooo` | Forward by `[1..512]` if true. |
| `JUMP BY -offset ON Fn`     | `0011nnnooooooooo` | Backward by `[1..512]` if true.|
|                     | | |
| **Flag Operations** | | |
| `CLEAR Fn`                  | `0100nnn*********` | Set flag `F[1..7C]` to false.  |
| `SET Fn`                    | `0101nnn*********` | Set flag `F[1..7C]` to true.   |
| `FLIP Fn`                   | `0110nnn*********` | Invert flag `F[1..7C]`.        |
|                           | | |
| **Unary Operations** | | |
| `Rz := RANDOM`              | `1000000******zzz` | Put a random number in `Rz`.   |
| `Rz := 1`                   | `1000001******zzz` | Put `1` in `Rz`.               |
| `Rz := Rx`                  | `1000010xxx***zzz` | Put the value of `Rx` in `Rz`. |
| `Rz := NOT Rx`              | `1000011xxx***zzz` | One's complement of `Rx`.      |
| `Rz := -Rx`                 | `1000100xxx***zzz` | Two's complement of `Rx`.      |
| `Rz := Rx <- FC`            | `1000101xxx***zzz` | `Rx` shifted left with carry.  |
| `Rz := FC -> Rx`            | `1000110xxx***zzz` | `Rx` shifted right with carry. |
| `Rz := FN -> Rx`            | `1000111xxx***zzz` | `Rx` shifted right with sign.  |
|                           | | |
| **Binary Operations** | | |
| `Rz := Rx + 1`              | `1001000xxx***zzz` | Put `Rx` plus `1` in `Rz`.     |
| `Rz := Rx - 1`              | `1001001xxx***zzz` | Put `Rx` minus `1` in `Rz`.    |
| `Rz := Rx + 2`              | `1001010xxx***zzz` | Put `Rx` plus `2` in `Rz`.     |
| `Rz := Rx - 2`              | `1001011xxx***zzz` | Put `Rx` minus `2` in `Rz`.    |
| `Rz := Rx + 4`              | `1001100xxx***zzz` | Put `Rx` plus `4` in `Rz`.     |
| `Rz := Rx - 4`              | `1001101xxx***zzz` | Put `Rx` minus `4` in `Rz`.    |
| `Rz := Rx + Ry + FC`        | `1001110xxx***zzz` | Put `Rx` plus `Ry` in `Rz`.    |
| `Rz := Rx - Ry - FC`        | `1001111xxx***zzz` | Put `Rx` minus `Ry` in `Rz`.   |
|                           | | |
| **Boolean Operations** | | |
| `Rz := FALSE`               | `1010000******zzz` | Put `0` in `Rz`.               |
| `Rz := Rx >> Ry`            | `1010001xxxyyyzzz` | `Rx` is greater than `Ry`.     |
| `Rz := Rx == Ry`            | `1010010xxxyyyzzz` | `Rx` is equal to `Ry`.         |
| `Rz := Rx >= Ry`            | `1010011xxxyyyzzz` | `Rx` is not less than to `Ry`. |
| `Rz := Rx << Ry`            | `1010100xxxyyyzzz` | `Rx` is less than `Ry`.        |
| `Rz := Rx <> Ry`            | `1010101xxxyyyzzz` | `Rx` is not equal to `Ry`.     |
| `Rz := Rx <= Ry`            | `1010110xxxyyyzzz` | `Rx` is not greater than `Ry`. |
| `Rz := TRUE`                | `1010111******zzz` | Put `-1` in `Rz`.              |
|                        | | |
| **Logical Operations** | | |
| `Rz := Rx AND Ry`           | `1011000xxxyyyzzz` | Perform a bit-wise AND.        |
| `Rz := NOT (Rx AND Ry)`     | `1011001xxxyyyzzz` | Perform a bit-wise NAND.       |
| `Rz := Rx SAN Ry`           | `1011010xxxyyyzzz` | Perform a bit-wise SAN.        |
| `Rz := NOT (Rx SAN Ry)`     | `1011011xxxyyyzzz` | Perform a bit-wise NSAN.       |
| `Rz := Rx IOR Ry`           | `1011100xxxyyyzzz` | Perform a bit-wise IOR.        |
| `Rz := NOT (Rx IOR Ry)`     | `1011101xxxyyyzzz` | Perform a bit-wise NIOR.       |
| `Rz := Rx XOR Ry`           | `1011110xxxyyyzzz` | Perform a bit-wise XOR.        |
| `Rz := NOT (Rx XOR Ry)`     | `1011111xxxyyyzzz` | Perform a bit-wise NXOR.       |
|                       | | |
| **Memory Operations** | | |
| `Rz <- INPUT`               | `1100001******zzz` | Read `Rz` from input.          |
| `Ry -> OUTPUT`              | `1101010***yyy***` | Write `Ry` to output.          |
| `Rz <- constant`            | `1110001******zzz` | Load `Rz` with constant.       |
| `Rz <- @(Rx)`               | `1110101xxx***zzz` | Load `Rz` from memory address. |
| `Ry -> @(Rx)`               | `1111110xxxyyy***` | Store `Ry` in memory address.  |
| `RESET`                     | `1111111111111111` | Reset CPU, I/O and memory.     |

### Getting Started
To include the Go packages for this module use the following import statement:
```go
import (
	pro "github.com/craterdog/go-simple-processor/v2"
)
```

### Contributing
Project contributors are always welcome. Check out the contributing guidelines
[here](https://github.com/craterdog/go-simple-processor/blob/main/.github/CONTRIBUTING.md).

<H5 align="center"> Copyright © 2009-2025. Crater Dog Technologies™. All rights reserved. </H5>
