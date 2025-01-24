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
 * `PC` {program counter}
 * `IR` {instruction register}
 * `Rn` {general purpose 1-8}

#### Memory
 * 1 word = 16 bits
 * 64K addressable words {0x0000 - 0xFFFF}
 * address 0x0000 is for I/O {readable and writable}
 * addresses 0x0001-0x0007 are the persistent, read-only boot instructions
 * addresses 0x0008-0xFFFF are random access memory (RAM)

### Instruction Set

| Mnemonic                    | Instruction        | Notes                          |
|-----------------------------|--------------------|--------------------------------|
| **Control Operations** | | |
| `SKIP`                      | `0000000000000000` | This instruction does nothing. |
| `JUMP BY offset`            | `00oooooooooooooo` | The offset is `[-8192..8191]`. |
| `JUMP BY offset ON Fn`      | `010nnnoooooooooo` | The offset is `[-512..511]`.   |
| `CLEAR Fn`                  | `0110nn**********` | `nn` maps to flags `[123C]`.   |
| `SET Fn`                    | `0111nn**********` | `nn` maps to flags `[123C]`.   |
| | | |
| **Assignment Operations** | | |
| `Rx := FLAGS`               | `1000000xxx***001` | The flags are `123CNZVI`.      |
| `Rx := PC`                  | `1000000xxx***010` |                                |
| `Rx := IR`                  | `1000000xxx***100` |                                |
| `Rx := Ry`                  | `1000001xxxyyy000` |                                |
| `Rx := FALSE`               | `1000010xxx****00` | The value of false is `0x0000`.|
| `Rx := TRUE`                | `1000010xxx****01` | The value of true is `0xFFFF`. |
| `Rx := RANDOM`              | `1000010xxx****10` | A random number is generated.  |
| `Rx := offset`              | `1000100xxxoooooo` | The offset is `[-32..31]`.     |
| `Rx := constant`            | `1000110xxx******` | A constant is in the next word.|
| | | |
| **Logical Operations** | | |
| `Rx := Ry AND Rz`           | `1001000xxxyyyzzz` |                                |
| `Rx := NOT (Ry AND Rz)`     | `1001001xxxyyyzzz` |                                |
| `Rx := Ry SAN Rz`           | `1001010xxxyyyzzz` |                                |
| `Rx := NOT (Ry SAN Rz)`     | `1001011xxxyyyzzz` |                                |
| `Rx := Ry IOR Rz`           | `1001100xxxyyyzzz` |                                |
| `Rx := NOT (Ry IOR Rz)`     | `1001101xxxyyyzzz` |                                |
| `Rx := Ry XOR Rz`           | `1001110xxxyyyzzz` |                                |
| `Rx := NOT (Ry NXOR Rz)`    | `1001111xxxyyyzzz` |                                |
| | | |
| **Relational Operations** | | |
| `Rx := Ry >> Rz`            | `1010001xxxyyyzzz` |                                |
| `Rx := Ry == Rz`            | `1010010xxxyyyzzz` |                                |
| `Rx := Ry >= Rz`            | `1010011xxxyyyzzz` |                                |
| `Rx := Ry << Rz`            | `1010100xxxyyyzzz` |                                |
| `Rx := Ry <> Rz`            | `1010101xxxyyyzzz` |                                |
| `Rx := Ry <= Rz`            | `1010110xxxyyyzzz` |                                |
| | | |
| **Arithmetic Operations** | | |
| `Rx := NOT Ry`              | `1011000xxxyyy**0` | Take the one's complement.     |
| `Rx := -Ry`                 | `1011000xxxyyy**1` | Take the two's complement.     |
| `Rx :=  0 -> Ry`            | `1011001xxxyyy000` |                                |
| `Rx := Ry <- 0`             | `1011001xxxyyy001` | Multiply by 2.                 |
| `Rx :=  1 -> Ry`            | `1011001xxxyyy010` |                                |
| `Rx := Ry <- 1`             | `1011001xxxyyy011` |                                |
| `Rx := FC -> Ry`            | `1011001xxxyyy100` |                                |
| `Rx := Ry <- FC`            | `1011001xxxyyy101` |                                |
| `Rx := FN -> Ry`            | `1011001xxxyyy110` | Divide by 2 w/ sign extension. |
| `Rx := Ry <- FN`            | `1011001xxxyyy111` |                                |
| `Rx := Ry -> offset`        | `1011010xxxyyyooo` | The offset range is `[1..8]`.  |
| `Rx := Ry <- offset`        | `1011011xxxyyyooo` | The offset range is `[1..8]`.  |
| `Rx := Ry + offset`         | `1011100xxxyyyooo` | The offset range is `[1..8]`.  |
| `Rx := Ry - offset`         | `1011101xxxyyyooo` | The offset range is `[1..8]`.  |
| `Rx := Ry + Rz + FC`        | `1011110xxxyyyzzz` |                                |
| `Rx := Ry - Rz - FC`        | `1011111xxxyyyzzz` |                                |
| | | |
| **Memory Operations** | | |
| `Rx <- @(Ry)`               | `1101100xxxyyy***` |                                |
| `Rx <- @(Ry + Rz)`          | `1101101xxxyyyzzz` |                                |
| `Rx <- @(Ry + offset)`      | `1101110xxxyyy***` | The offset is in the next word.|
| `Rx <- @(Ry + offset + Rz)` | `1101111xxxyyyzzz` | The offset is in the next word.|
| `Rx -> @(Ry)`               | `1110100xxxyyy***` |                                |
| `Rx -> @(Ry + Rz)`          | `1110101xxxyyyzzz` |                                |
| `Rx -> @(Ry + offset)`      | `1110110xxxyyy***` | The offset is in the next word.|
| `Rx -> @(Ry + offset + Rz)` | `1110111xxxyyyzzz` | The offset is in the next word.|
| `RESET`                     | `1111************` | The processor, I/O and memory. |

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
