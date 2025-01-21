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
 * `N` {negative}
 * `Z` {zero}
 * `C` {carry}
 * `V` {overflow}
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
 * addresses 0x0001-0x0007 are the persistent, read-only boot ROM instructions
 * addresses 0x0008-0xFFFF are the RAM

### Instruction Set

#### Branch Operations
`[00][c][fff][oooooooooo]`
 * `SKIP` {all zeros does nothing}
 * `JUMP BY fffoooooooooo` {`fffooooooooo` in the range [-4096..4095]}
 * `JUMP BY oooooooooo ON fff` {`ooooooooo` in the range [-512..511]}

#### Program Control
`[01][opr][fff][00000000]`
 * `CLEAR fff
 * `SET fff
 * `HALT`
 * `HALT ON fff`
 * `WAIT ON INPUT`
 * `WAIT ON OUTPUT`

#### Assignment Operations
`[10][00][opr][iii][jjj][kkk]`
 * `Ri := FLAGS` {into the lower byte of `Ri`}
 * `Ri := FALSE` {0x0000}
 * `Ri := TRUE`  {0xFFFF}
 * `Ri := RANDOM`
 * `Ri := jjjkkk` {`jjjkkk` in the range [-32..31]}
 * `Ri := constant` {this is a two word instruction}
 * `Ri := Rj`

#### Logical Operations
`[10][01][opr][iii][jjj][kkk]`
 * `Ri := Rj AND Rk`
 * `Ri := Rj SAN Rk`
 * `Ri := Rj IOR Rk`
 * `Ri := Rj XOR Rk`
 * `Ri := Rj NAND Rk`
 * `Ri := Rj NSAN Rk`
 * `Ri := Rj NIOR Rk`
 * `Ri := Rj NXOR Rk`

#### Relational Operations
`[10][10][opr][iii][jjj][kkk]`
 * `Ri := Rj >> Rk`
 * `Ri := Rj == Rk`
 * `Ri := Rj >= Rk`
 * `Ri := Rj << Rk`
 * `Ri := Rj <> Rk`
 * `Ri := Rj <= Rk`

#### Arithmetic Operations
`[10][11][opr][iii][jjj][kkk]` {`kk` is 0, 1, C, or N; and `k` is C, or V}
 * `Ri := kk -> Rj -> k`
 * `Ri := k <- Rj <- kk`
 * `Ri := !Rj`
 * `Ri := -Rj`
 * `Ri := Rj + 1`
 * `Ri := Rj - 1`
 * `Ri := Rj + Rk + C`
 * `Ri := Rj - Rk - C`

#### Access Memory
`[11][rw][jok][iii][jjj][kkk]`
 * `Ri <- @(Rj)`
 * `Ri <- @(Rj + Rk)`
 * `Ri <- @(Rj + offset)` {this is a two word instruction}
 * `Ri <- @(Rj + offset + Rk)` {this is a two word instruction}
 * `Ri -> @(Rj)`
 * `Ri -> @(Rj + Rk)`
 * `Ri -> @(Rj + offset)` {this is a two word instruction}
 * `Ri -> @(Rj + offset + Rk)` {this is a two word instruction}

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
