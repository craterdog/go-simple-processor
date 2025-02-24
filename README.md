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
 * `ST` {status register containing system and user flags}

#### Memory
 * 64K of addressable words {`0x0000` - `0xFFFF`}
 * 1 word = 16 bits
 * Address `0x0000` is for I/O {readable and writable}
 * Addresses `0x0001` - `0x0007` are the persistent, read-only boot instructions
 * Addresses `0x0008` - `0xFFFF` are random access memory (RAM)

### Instruction Set
The instruction set for this processor consists of 40 instructions and one
pseudo instruction (`SKIP`) which is equivalent to the `JUMP BY 1` instruction
(the traditional "NOP" instruction).  The `Rz` register cannot be any of the
system registers to ensure the integrity of the processor.  The `Rx` and `Ry`
registers may be any of the eight registers.  Similarly, only the user status
flags may be set or cleared, again to ensure the integrity of the processor.

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
| `CLEAR Fn`                  | `0100nnn*********` | Set flag `[1..8]` to false.    |
| `SET Fn`                    | `0101nnn*********` | Set flag `[1..8]` to true.     |
|                           | | |
| **Assignment Operations** | | |
| `Rz += offset`              | `10000000ooooozzz` | Increment `Rz` by `[1..32]`.   |
| `Rz -= offset`              | `10000001ooooozzz` | Decrement `Rz` by `[1..32]`.   |
| `Rz := 0`                   | `1000001******zzz` | Reset `Rz` to zero.            |
| `Rz := RANDOM`              | `1000010******zzz` | Put a random number in `Rz`.   |
| `Rz := Rx`                  | `1000011xxx***zzz` | Save `Rx` in `Rz`.             |
| `Rz := constant`            | `100010ccccccczzz` | Set `Rz` to `[1..128]`.        |
| `Rz := -constant`           | `100011ccccccczzz` | Set `Rz` to `-[1..128]`.       |
|                        | | |
| **Logical Operations** | | |
| `Rz := Rx AND Ry`           | `1001000xxxyyyzzz` | Perform a logical AND.         |
| `Rz := NOT (Rx AND Ry)`     | `1001001xxxyyyzzz` | Perform a logical NAND.        |
| `Rz := Rx SAN Ry`           | `1001010xxxyyyzzz` | Perform a logical SAN.         |
| `Rz := NOT (Rx SAN Ry)`     | `1001011xxxyyyzzz` | Perform a logical NSAN.        |
| `Rz := Rx IOR Ry`           | `1001100xxxyyyzzz` | Perform a logical OR.          |
| `Rz := NOT (Rx IOR Ry)`     | `1001101xxxyyyzzz` | Perform a logical NOR.         |
| `Rz := Rx XOR Ry`           | `1001110xxxyyyzzz` | Perform a logical XOR.         |
| `Rz := NOT (Rx XOR Ry)`     | `1001111xxxyyyzzz` | Perform a logical NXOR.        |
|                           | | |
| **Relational Operations** | | |
| `Rz := Rx >> Ry`            | `1010001xxxyyyzzz` | Is greater than.               |
| `Rz := Rx == Ry`            | `1010010xxxyyyzzz` | Is equal to.                   |
| `Rz := Rx >= Ry`            | `1010011xxxyyyzzz` | Is greater than or equal to.   |
| `Rz := Rx << Ry`            | `1010100xxxyyyzzz` | Is less than.                  |
| `Rz := Rx <> Ry`            | `1010101xxxyyyzzz` | Is not equal to.               |
| `Rz := Rx <= Ry`            | `1010110xxxyyyzzz` | Is less than or equal to.      |
|                           | | |
| **Arithmetic Operations** | | |
| `Rz := NOT Rx`              | `1011000xxx***zzz` | One's complement of `Rx`.      |
| `Rz := -Rx`                 | `1011001xxx***zzz` | Two's complement of `Rx`.      |
| `Rz := FC -> Rx`            | `1011010xxx***zzz` | `Rx` shifted right with carry. |
| `Rz := Rx <- FC`            | `1011011xxx***zzz` | `Rx` shifted left with carry.  |
| `Rz := FN -> Rx`            | `1011100xxx***zzz` | Divide `Rx` by `2`.            |
| `Rz := Rx + offset`         | `1011101xxxooozzz` | Add offset of `[1..8]`.        |
| `Rz := Rx - offset`         | `1011110xxxooozzz` | Subtract offset of `[1..8]`.   |
| `Rz := Rx + Ry + FC`        | `1011111xxxyyyzzz` | Addition of `Rx` and `Ry`.     |
|                       | | |
| **Memory Operations** | | |
| `Rz <- INPUT`               | `1100001******zzz` | Read `Rz` from input.          |
| `Ry -> OUTPUT`              | `1101010***yyy***` | Write `Ry` to output.          |
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
