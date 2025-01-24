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
 * `V` {overflow = N XOR C}
 * `N` {negative}
 * `Z` {zero}
 * `I` {input available}

#### Registers
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
| *Unconditional Jump* | | |
| `SKIP`                      | `0000000000000000` | This instruction does nothing. |
| `JUMP BY offset`            | `00oooooooooooooo` | The offset is [-8192..8191].   |
| | | |
| *Conditional Branch* | | |
| `CLEAR flag`                | `0100ff0000000000` | `ff` maps to flags `123C`.     |
| `SET flag`                  | `0101ff0000000000` | `ff` maps to flags `123C`.     |
| `BRANCH BY offset ON flag`  | `011fffoooooooooo` | The offset is [-512..511].     |
| | | |
| *Assignment Operation* | | |
| `Ri := FLAGS`               | `1000000iii000001` | The flags are `123CVNZI`.      |
| `Ri := PC`                  | `1000000iii000010` |                                |
| `Ri := IR`                  | `1000000iii000100` |                                |
| `Ri := Rj`                  | `1000001iiijjj000` |                                |
| `Ri := FALSE`               | `1000010iii000000` | The value of false is `0x0000`.|
| `Ri := TRUE`                | `1000010iii000001` | The value of true is `0xFFFF`. |
| `Ri := RANDOM`              | `1000010iii000010` | A random number is generated.  |
| `Ri := offset`              | `1000100iiioooooo` | The offset is [-32..31].       |
| `Ri := constant`            | `1000110iii000000` | A constant is in the next word.|
| | | |
| *Logical Operation* | | |
| `Ri := Rj AND Rk`           | `1001000iiijjjkkk` |                                |
| `Ri := Rj NAND Rk`          | `1001001iiijjjkkk` |                                |
| `Ri := Rj SAN Rk`           | `1001010iiijjjkkk` |                                |
| `Ri := Rj NSAN Rk`          | `1001011iiijjjkkk` |                                |
| `Ri := Rj IOR Rk`           | `1001100iiijjjkkk` |                                |
| `Ri := Rj NIOR Rk`          | `1001101iiijjjkkk` |                                |
| `Ri := Rj XOR Rk`           | `1001110iiijjjkkk` |                                |
| `Ri := Rj NXOR Rk`          | `1001111iiijjjkkk` |                                |
| | | |
| *Relational Operation* | | |
| `Ri := Rj >> Rk`            | `1010001iiijjjkkk` |                                |
| `Ri := Rj == Rk`            | `1010010iiijjjkkk` |                                |
| `Ri := Rj >= Rk`            | `1010011iiijjjkkk` |                                |
| `Ri := Rj << Rk`            | `1010100iiijjjkkk` |                                |
| `Ri := Rj <> Rk`            | `1010101iiijjjkkk` |                                |
| `Ri := Rj <= Rk`            | `1010110iiijjjkkk` |                                |
| | | |
| *Arithmetic Operation* | | |
| `Ri := !Rj`                 | `1011000iiijjj000` | Take the one's complement.     |
| `Ri := -Rj`                 | `1011000iiijjj001` | Take the two's complement.     |
| `Ri := 0 -> Rj`             | `1011001iiijjj000` |                                |
| `Ri := Rj <- 0`             | `1011001iiijjj001` | Multiply by 2.                 |
| `Ri := 1 -> Rj`             | `1011001iiijjj010` |                                |
| `Ri := Rj <- 1`             | `1011001iiijjj011` |                                |
| `Ri := C -> Rj`             | `1011001iiijjj100` |                                |
| `Ri := Rj <- C`             | `1011001iiijjj101` |                                |
| `Ri := N -> Rj`             | `1011001iiijjj110` | Divide by 2 w/ sign extension. |
| `Ri := Rj <- N`             | `1011001iiijjj111` |                                |
| `Ri := Rj -> offset`        | `1011010iiijjjooo` | The offset range is [1..8].    |
| `Ri := Rj <- offset`        | `1011011iiijjjooo` | The offset range is [1..8].    |
| `Ri := Rj + offset`         | `1011100iiijjjooo` | The offset range is [1..8].    |
| `Ri := Rj - offset`         | `1011101iiijjjooo` | The offset range is [1..8].    |
| `Ri := Rj + Rk + C`         | `1011110iiijjjkkk` |                                |
| `Ri := Rj - Rk - C`         | `1011111iiijjjkkk` |                                |
| | | |
| *Memory Access* | | |
| `Ri <- @(Rj)`               | `1101100iiijjj000` |                                |
| `Ri <- @(Rj + Rk)`          | `1101101iiijjjkkk` |                                |
| `Ri <- @(Rj + offset)`      | `1101110iiijjj000` | The offset is in next word.    |
| `Ri <- @(Rj + offset + Rk)` | `1101111iiijjjkkk` | The offset is in next word.    |
| `Ri -> @(Rj)`               | `1110100iiijjj000` |                                |
| `Ri -> @(Rj + Rk)`          | `1110101iiijjjkkk` |                                |
| `Ri -> @(Rj + offset)`      | `1110110iiijjj000` | The offset is in next word.    |
| `Ri -> @(Rj + offset + Rk)` | `1110111iiijjjkkk` | The offset is in next word.    |
| `RESET`                     | `1111111111111111` | The processor, I/O and memory. |

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
