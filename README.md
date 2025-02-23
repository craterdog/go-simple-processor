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

#### Flags
 * `Fn` {general purpose flags: `F1` - `F3`}
 * `FC` {carry flag}
 * `FN` {negative flag}
 * `FZ` {zero flag}
 * `FV` {overflow flag: `FN XOR FC`}
 * `FI` {input is available flag}

#### Registers
 * `Rn` {general purpose registers: `R1` - `R6`}
 * `IR` {instruction register: `R0`}
 * `PC` {program counter: `R7`}

#### Memory
 * 64K of addressable words {`0x0000` - `0xFFFF`}
 * 1 word = 16 bits
 * Address `0x0000` is for I/O {readable and writable}
 * Addresses `0x0001` - `0x0007` are the persistent, read-only boot instructions
 * Addresses `0x0008` - `0xFFFF` are random access memory (RAM)

### Instruction Set

| Mnemonic                    | Instruction        | Notes                          |
|-----------------------------|--------------------|--------------------------------|
| **Control Operations** | | |
| `SKIP`                      | `0000000000000000` | This instruction does nothing. |
| `JUMP BY offset`            | `0000oooooooooooo` | The offset is `[1..4096]`.     |
| `JUMP BY -offset`           | `0001oooooooooooo` | The offset is `[1..4096]`.     |
| `JUMP BY offset ON Fn`      | `0010nnnooooooooo` | The offset is `[1..512]`.      |
| `JUMP BY -offset ON Fn`     | `0011nnnooooooooo` | The offset is `[1..512]`.      |
|                     | | |
| **Flag Operations** | | |
| `CLEAR Fn`                  | `01000nn*********` | `nn` maps to flags `F[123C]`.  |
| `SET Fn`                    | `01100nn*********` | `nn` maps to flags `F[123C]`.  |
|                           | | |
| **Assignment Operations** | | |
| `Rx := 0`                   | `1000000******xxx` | Reset register.                |
| `Rx := FLAGS`               | `1000001******xxx` | The flags map to `0x00FF`.     |
| `Rx := RANDOM`              | `1000010******xxx` | A random number is generated.  |
| `Rx := Ry`                  | `1000011***yyyxxx` | `Ry` includes `PC` and `IR`.   |
| `Rx := constant`            | `100010cccccccxxx` | The constant is `[1..128]`.    |
| `Rx := -constant`           | `100011cccccccxxx` | The constant is `[1..128]`.    |
|                        | | |
| **Logical Operations** | | |
| `Rx := Ry AND Rz`           | `1001000zzzyyyxxx` | Logical AND.                   |
| `Rx := NOT (Ry AND Rz)`     | `1001001zzzyyyxxx` | Logical NAND.                  |
| `Rx := Ry SAN Rz`           | `1001010zzzyyyxxx` | Logical SAN.                   |
| `Rx := NOT (Ry SAN Rz)`     | `1001011zzzyyyxxx` | Logical NSAN.                  |
| `Rx := Ry IOR Rz`           | `1001100zzzyyyxxx` | Logical OR.                    |
| `Rx := NOT (Ry IOR Rz)`     | `1001101zzzyyyxxx` | Logical NOR.                   |
| `Rx := Ry XOR Rz`           | `1001110zzzyyyxxx` | Logical XOR.                   |
| `Rx := NOT (Ry XOR Rz)`     | `1001111zzzyyyxxx` | Logical NXOR.                  |
|                           | | |
| **Relational Operations** | | |
| `Rx := Ry >> Rz`            | `1010001zzzyyyxxx` | Greater than.                  |
| `Rx := Ry == Rz`            | `1010010zzzyyyxxx` | Equal to.                      |
| `Rx := Ry >= Rz`            | `1010011zzzyyyxxx` | Greater than or equal to.      |
| `Rx := Ry << Rz`            | `1010100zzzyyyxxx` | Less than.                     |
| `Rx := Ry <> Rz`            | `1010101zzzyyyxxx` | Not equal to.                  |
| `Rx := Ry <= Rz`            | `1010110zzzyyyxxx` | Less than or equal to.         |
|                           | | |
| **Arithmetic Operations** | | |
| `Rx := NOT Ry`              | `1011000***yyyxxx` | Take the one's complement.     |
| `Rx := -Ry`                 | `1011001***yyyxxx` | Take the two's complement.     |
| `Rx := FC -> Ry`            | `1011010***yyyxxx` | Shift right with carry.        |
| `Rx := Ry <- FC`            | `1011011***yyyxxx` | Shift left with carry.         |
| `Rx := FN -> Ry`            | `1011100***yyyxxx` | Divide by 2 w/ sign extension. |
| `Rx := Ry + Rz + FC`        | `1011101zzzyyyxxx` | Addition with carry.           |
| `Rx := Ry + offset`         | `1011110oooyyyxxx` | The offset range is `[1..8]`.  |
| `Rx := Ry - offset`         | `1011111oooyyyxxx` | The offset range is `[1..8]`.  |
|                       | | |
| **Memory Operations** | | |
| `Rx <- INPUT`               | `11000********xxx` | Load from input.               |
| `Rx <- @(Ry)`               | `11010*****yyyxxx` | Load from memory address.      |
| `Rx -> OUTPUT`              | `11100********xxx` | Store to output.               |
| `Rx -> @(Ry)`               | `11110*****yyyxxx` | Store to memory address.       |
| `RESET`                     | `1111111111111111` | Processor, I/O and memory.     |

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
