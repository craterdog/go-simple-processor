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
| `CLEAR Fn`                  | `01000nn*********` | Set flag `F[123C]` to false.   |
| `SET Fn`                    | `01001nn*********` | Set flag `F[123C]` to true.    |
| `Rx := FLAGS`               | `01010********xxx` | Flags map to `0x00FF` in `Rx`. |
| `FLAGS := Rx`               | `01011***********` | Flags map to `0x00FF` in `Rx`. |
|                           | | |
| **Assignment Operations** | | |
| `Rx := 0`                   | `1000000******xxx` | Reset the `Rx`.                |
| `Rx := RANDOM`              | `1000001******xxx` | Put a random number in `Rx`.   |
| `Rx := Ry`                  | `1000010***yyyxxx` | `Ry` includes `PC` and `IR`.   |
| `Rx := Rx + offset`         | `10000110oooooxxx` | Increment `Rx` by `[1..32]`.   |
| `Rx := Rx - offset`         | `10000111oooooxxx` | Decrement `Rx` by `[1..32]`.   |
| `Rx := constant`            | `100010cccccccxxx` | Set `Rx` to `[1..128]`.        |
| `Rx := -constant`           | `100011cccccccxxx` | Set `Rx` to `-[1..128]`.       |
|                        | | |
| **Logical Operations** | | |
| `Rx := Ry AND Rz`           | `1001000zzzyyyxxx` | Perform a logical AND.         |
| `Rx := NOT (Ry AND Rz)`     | `1001001zzzyyyxxx` | Perform a logical NAND.        |
| `Rx := Ry SAN Rz`           | `1001010zzzyyyxxx` | Perform a logical SAN.         |
| `Rx := NOT (Ry SAN Rz)`     | `1001011zzzyyyxxx` | Perform a logical NSAN.        |
| `Rx := Ry IOR Rz`           | `1001100zzzyyyxxx` | Perform a logical OR.          |
| `Rx := NOT (Ry IOR Rz)`     | `1001101zzzyyyxxx` | Perform a logical NOR.         |
| `Rx := Ry XOR Rz`           | `1001110zzzyyyxxx` | Perform a logical XOR.         |
| `Rx := NOT (Ry XOR Rz)`     | `1001111zzzyyyxxx` | Perform a logical NXOR.        |
|                           | | |
| **Relational Operations** | | |
| `Rx := Ry >> Rz`            | `1010001zzzyyyxxx` | Is greater than.               |
| `Rx := Ry == Rz`            | `1010010zzzyyyxxx` | Is equal to.                   |
| `Rx := Ry >= Rz`            | `1010011zzzyyyxxx` | Is greater than or equal to.   |
| `Rx := Ry << Rz`            | `1010100zzzyyyxxx` | Is less than.                  |
| `Rx := Ry <> Rz`            | `1010101zzzyyyxxx` | Is not equal to.               |
| `Rx := Ry <= Rz`            | `1010110zzzyyyxxx` | Is less than or equal to.      |
|                           | | |
| **Arithmetic Operations** | | |
| `Rx := NOT Ry`              | `1011000***yyyxxx` | One's complement of `Ry`.      |
| `Rx := -Ry`                 | `1011001***yyyxxx` | Two's complement of `Ry`.      |
| `Rx := FC -> Ry`            | `1011010***yyyxxx` | `Ry` shifted right with carry. |
| `Rx := Ry <- FC`            | `1011011***yyyxxx` | `Ry` shifted left with carry.  |
| `Rx := FN -> Ry`            | `1011100***yyyxxx` | Divide `Ry` by `2`.            |
| `Rx := Ry + Rz + FC`        | `1011101zzzyyyxxx` | Addition of `Ry` and `Rz`.     |
| `Rx := Ry + offset`         | `1011110oooyyyxxx` | Add offset of `[1..8]`.        |
| `Rx := Ry - offset`         | `1011111oooyyyxxx` | Subtract offset of `[1..8]`.   |
|                       | | |
| **Memory Operations** | | |
| `Rx <- INPUT`               | `11000********xxx` | Read `Rx` from input.          |
| `Rx <- @(Ry)`               | `11010*****yyyxxx` | Load `Rx` from memory address. |
| `Rx -> OUTPUT`              | `11100********xxx` | Write `Rx` to output.          |
| `Rx -> @(Ry)`               | `11110*****yyyxxx` | Store `Rx` in memory address.  |
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
