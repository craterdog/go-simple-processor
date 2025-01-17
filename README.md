<img src="https://craterdog.com/images/CraterDog.png" width="50%">

## Go Simple Processor Simulator

### Overview
This project defines the design and instruction set for a simple 16 bit
processor.  It also provides a Go based simulator for the processor.

### Architecture

#### Flags
 * `F1` {general purpose}
 * `F2` {general purpose}
 * `F3` {general purpose}
 * `Fn` {negative}
 * `Fz` {zero}
 * `Fc` {carry}
 * `Fv` {overflow}
 * `Fi` {interrupt}

#### Registers
 * `Rn` {general purpose registers 1-8}
 * `PC` {program counter}
 * `IR` {instruction register}
 * `AR` {address register}
 * `DR` {data register}

#### Memory
 * 1 word = 16 bits
 * 64K addressable words {0x0000 - 0xFFFF}
 * address 0xFFFF is for I/O {readable and writable}

### Instruction Set

#### Program Control
`[00][op][fff][ooooooooo]`
 * `SKIP` {all zeros does nothing}
 * `JUMP BY fffooooooooo` {`fffooooooooo` in the range [-2048..2047]}
 * `JUMP BY ooooooooo ON fff` {`ooooooooo` in the range [-256..255]}
 * `HALT`
 * `HALT ON fff`

#### Assignment Operations
`[01][00][opr][iii][jjj][kkk]`
 * `FLAGS := Ri` {lower byte of `Ri` only}
 * `Ri := FLAGS` {lower byte of `Ri` only}
 * `Ri := FALSE`
 * `Ri := TRUE`
 * `Ri := RANDOM`
 * `Ri := jjjkkk` {`jjjkkk` in the range [-32..31]}
 * `Ri := constant` {this is a two word instruction}
 * `Ri := Rj`

#### Logical Operations
`[01][01][opr][iii][jjj][kkk]`
 * `Ri := Rj AND Rk`
 * `Ri := Rj SAN Rk`
 * `Ri := Rj IOR Rk`
 * `Ri := Rj XOR Rk`
 * `Ri := Rj NAND Rk`
 * `Ri := Rj NSAN Rk`
 * `Ri := Rj NIOR Rk`
 * `Ri := Rj NXOR Rk`

#### Relational Operations
`[01][10][opr][iii][jjj][kkk]`
 * `Ri := Rj >> Rk`
 * `Ri := Rj == Rk`
 * `Ri := Rj >= Rk`
 * `Ri := Rj << Rk`
 * `Ri := Rj <> Rk`
 * `Ri := Rj <= Rk`

#### Arithmetic Operations
`[01][11][opr][iii][jjj][kkk]` {`kk` is 0, 1, C, N; and `k` is C, V}
 * `Ri := kk -> Rj -> k`
 * `Ri := k <- Rj <- kk`
 * `Ri := !Rj`
 * `Ri := -Rj`
 * `Ri := Rj + 1`
 * `Ri := Rj - 1`
 * `Ri := Rj + Rk + C`
 * `Ri := Rj - Rk - C`

#### Access Memory
`[10][rw][jok][iii][jjj][kkk]`
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
