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
 * `1` {general purpose}
 * `2` {general purpose}
 * `3` {general purpose}
 * `C` {carry}
 * `V` {overflow}
 * `N` {negative}
 * `Z` {zero}
 * `I` {input available}

#### Registers
 * `AR` {memory address register}
 * `DR` {memory data register}
 * `PC` {program counter}
 * `IR` {instruction register}
 * `Rn` {general purpose registers 1-8}

#### Memory
 * 1 word = 16 bits
 * 64K addressable words {0x0000 - 0xFFFF}
 * address 0x0000 is for I/O {readable and writable}
 * addresses 0x0001-0x0007 are the persistent, read-only boot instructions
 * addresses 0x0008-0xFFFF are random access memory (RAM)

### Instruction Set

| Mnemonic                    | Instruction        | Notes                          |
|-----------------------------|--------------------|--------------------------------|
| *Unconditional Branch* | | |
| `SKIP`                      | `0000000000000000` | This instruction does nothing. |
| `JUMP BY offset`            | `00oooooooooooooo` | The offset is [-8192..8191].   |
| *Conditional Branch* | | |
| `CLEAR flag`                | `0100ff0000000000` | `ff` maps to `123C`.           |
| `Set flag`                  | `0101ff0000000000` | `ff` maps to `123C`.           |
| `BRANCH BY offset ON flag`  | `011fffoooooooooo` | The offset is [-512..511].     |
| *Assignment Operations* | | |
| `Ri := FLAGS`               | `1000000iii000000` | `123CVNZI`                     |
| `Ri := AR`                  | `1000000iii000001` |                                |
| `Ri := DR`                  | `1000000iii000010` |                                |
| `Ri := PC`                  | `1000000iii000011` |                                |
| `Ri := IR`                  | `1000000iii000100` |                                |
| `Ri := Rj`                  | `1000001iiijjj000` |                                |
| `Ri := FALSE`               | `1000010iii000000` | `0x0000`                       |
| `Ri := TRUE`                | `1000010iii000001` | `0xFFFF`                       |
| `Ri := RANDOM`              | `1000010iii000010` | A random number is generated.  |
| `Ri := offset`              | `1000100iiioooooo` | The offset is [-32..31].       |
| `Ri := constant`            | `1000110iii000000` | The constant is in next word.  |
| *Logical Operations* | | |
| `Ri := Rj AND Rk`           | `1001000iiijjjkkk` |                                |
| `Ri := Rj SAN Rk`           | `1001001iiijjjkkk` |                                |
| `Ri := Rj IOR Rk`           | `1001010iiijjjkkk` |                                |
| `Ri := Rj XOR Rk`           | `1001011iiijjjkkk` |                                |
| `Ri := Rj NAND Rk`          | `1001100iiijjjkkk` |                                |
| `Ri := Rj NSAN Rk`          | `1001101iiijjjkkk` |                                |
| `Ri := Rj NIOR Rk`          | `1001110iiijjjkkk` |                                |
| `Ri := Rj NXOR Rk`          | `1001111iiijjjkkk` |                                |
| *Relational Operations* | | |
| `Ri := Rj >> Rk`            | `1010001iiijjjkkk` |                                |
| `Ri := Rj == Rk`            | `1010010iiijjjkkk` |                                |
| `Ri := Rj >= Rk`            | `1010011iiijjjkkk` |                                |
| `Ri := Rj << Rk`            | `1010100iiijjjkkk` |                                |
| `Ri := Rj <> Rk`            | `1010101iiijjjkkk` |                                |
| `Ri := Rj <= Rk`            | `1010110iiijjjkkk` |                                |
| *Arithmetic Operations* | | |
| `Ri := ll -> Rj -> r`       | `1011000iiijjjllr` |                                |
| `Ri := l <- Rj <- rr`       | `1011001iiijjjlrr` |                                |
| `Ri := !Rj`                 | `1011010iiijjj000` |                                |
| `Ri := -Rj`                 | `1011011iiijjj000` |                                |
| `Ri := Rj + offset`         | `1011100iiijjjooo` | The offset is [-4..3].         |
| `Ri := Rj - offset`         | `1011101iiijjjooo` | The offset is [-4..3].         |
| `Ri := Rj + Rk + C`         | `1011110iiijjjkkk` |                                |
| `Ri := Rj - Rk - C`         | `1011111iiijjjkkk` |                                |
| *Memory Access* | | |
| `Ri <- @(Rj)`               | `1101100iiijjj000` |                                |
| `Ri <- @(Rj + Rk)`          | `1101101iiijjjkkk` |                                |
| `Ri <- @(Rj + offset)`      | `1101110iiijjj000` | The offset is in next word.    |
| `Ri <- @(Rj + offset + Rk)` | `1101111iiijjjkkk` | The offset is in next word.    |
| `Ri -> @(Rj)`               | `1110100iiijjj000` |                                |
| `Ri -> @(Rj + Rk)`          | `1110101iiijjjkkk` |                                |
| `Ri -> @(Rj + offset)`      | `1110110iiijjj000` | The offset is in next word.    |
| `Ri -> @(Rj + offset + Rk)` | `1110111iiijjjkkk` | The offset is in next word.    |

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
