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
 * `Fn` {general purpose 1-3}
 * `FC` {carry}
 * `FN` {negative}
 * `FZ` {zero}
 * `FV` {overflow = `FN XOR FC`}
 * `FI` {input is available}

#### Registers
 * `Rn` {general purpose 1-6}
 * `IR` {instruction register = R0}
 * `PC` {program counter = R7}

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
| `JUMP BY offset`            | `000ooooooooooooo` | The offset is `[-4096..4095]`. |
| `JUMP BY offset ON Fn`      | `001nnnoooooooooo` | The offset is `[-512..511]`.   |
| | | |
| **Assignment Operations** | | |
| `Fn := FALSE`               | `10000nn000000000` | `nn` maps to flags `[123C]`.   |
| `Fn := TRUE`                | `10000nn111111111` | `nn` maps to flags `[123C]`.   |
| `Rx := FLAGS`               | `1000000000000xxx` | The flags map to `0x00FF`.     |
| `Rx := FALSE`               | `1000001000000xxx` | The value of false is `0x0000`.|
| `Rx := TRUE`                | `1000001111111xxx` | The value of true is `0xFFFF`. |
| `Rx := RANDOM`              | `1000010000000xxx` | A random number is generated.  |
| `Rx := Ry`                  | `1000011000yyyxxx` | `Ry` includes `PC` and `IR`.   |
| `Rx := constant`            | `10001ccccccccxxx` | The constant is `[-128..127]`. |
| | | |
| **Logical Operations** | | |
| `Rx := Ry AND Rz`           | `1001000zzzyyyxxx` |                                |
| `Rx := NOT (Ry AND Rz)`     | `1001001zzzyyyxxx` |                                |
| `Rx := Ry SAN Rz`           | `1001010zzzyyyxxx` |                                |
| `Rx := NOT (Ry SAN Rz)`     | `1001011zzzyyyxxx` |                                |
| `Rx := Ry IOR Rz`           | `1001100zzzyyyxxx` |                                |
| `Rx := NOT (Ry IOR Rz)`     | `1001101zzzyyyxxx` |                                |
| `Rx := Ry XOR Rz`           | `1001110zzzyyyxxx` |                                |
| `Rx := NOT (Ry NXOR Rz)`    | `1001111zzzyyyxxx` |                                |
| | | |
| **Relational Operations** | | |
| `Rx := Ry >> Rz`            | `1010001zzzyyyxxx` |                                |
| `Rx := Ry == Rz`            | `1010010zzzyyyxxx` |                                |
| `Rx := Ry >= Rz`            | `1010011zzzyyyxxx` |                                |
| `Rx := Ry << Rz`            | `1010100zzzyyyxxx` |                                |
| `Rx := Ry <> Rz`            | `1010101zzzyyyxxx` |                                |
| `Rx := Ry <= Rz`            | `1010110zzzyyyxxx` |                                |
| | | |
| **Arithmetic Operations** | | |
| `Rx := NOT Ry`              | `1011000000yyyxxx` | Take the one's complement.     |
| `Rx := -Ry`                 | `1011000111yyyxxx` | Take the two's complement.     |
| `Rx :=  0 -> Ry`            | `1011001000yyyxxx` |                                |
| `Rx := Ry <- 0`             | `1011001111yyyxxx` | Multiply by 2.                 |
| `Rx :=  1 -> Ry`            | `1011010000yyyxxx` |                                |
| `Rx := Ry <- 1`             | `1011010111yyyxxx` |                                |
| `Rx := FC -> Ry`            | `1011011000yyyxxx` |                                |
| `Rx := Ry <- FC`            | `1011011111yyyxxx` |                                |
| `Rx := FN -> Ry`            | `1011100000yyyxxx` | Divide by 2 w/ sign extension. |
| `Rx := Ry <- FN`            | `1011100111yyyxxx` |                                |
| `Rx := Ry + Rz + FC`        | `1011101zzzyyyxxx` |                                |
| `Rx := Ry + offset`         | `1011110oooyyyxxx` | The offset range is `[1..8]`.  |
| `Rx := Ry - offset`         | `1011111oooyyyxxx` | The offset range is `[1..8]`.  |
| | | |
| **Memory Operations** | | |
| `Rx <- INPUT`               | `1100000000000xxx` |                                |
| `Rx <- @(Ry)`               | `1101000000yyyxxx` |                                |
| `Rx <- @(PC)`               | `1101000000111xxx` |                                |
| `Rx -> OUTPUT`              | `1110000000000xxx` |                                |
| `Rx -> @(Ry)`               | `1111000000yyyxxx` |                                |
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
