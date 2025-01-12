<img src="https://craterdog.com/images/CraterDog.png" width="50%">

## Go Simple Processor Simulator

### Overview
This project defines the design and instruction set for a simple 16 bit
processor.  It also provides a Go based simulator for the processor.

### Instruction Set

#### Process Control
 * `SKIP`
 * `JUMP offset`
 * `JUMP ON flags offset`

#### Register Operations
 * `Ri := 1`
 * `Ri := FALSE`
 * `Ri := TRUE`
 * `Ri := RANDOM`
 * `Ri := literal` {two word instruction}
 * `Ri := offset`
 * `Ri := Rj`
 * `Ri := ~ Rj`
 * `Ri := - Rj`
 * `Ri := N > Rj`
 * `Ri := C > Rj`
 * `Ri := Rj < C`
 * `Ri := Rj << Rk`
 * `Ri := Rj <= Rk`
 * `Ri := Rj == Rk`
 * `Ri := Rj >> Rk`
 * `Ri := Rj >= Rk`
 * `Ri := Rj <> Rk`
 * `Ri := Rj AND Rk`
 * `Ri := Rj SAN Rk`
 * `Ri := Rj OR Rk`
 * `Ri := Rj XOR Rk`
 * `Ri := Rj NAND Rk`
 * `Ri := Rj NSAN Rk`
 * `Ri := Rj NOR Rk`
 * `Ri := Rj NXOR Rk`
 * `Ri := C + Rj + offset`
 * `Ri := C + Rj + Rk`

#### Memory Access
 * `Ri := @(Rj + Rk + offset)` {two word instruction}
 * `@(Ri + Rk + offset) := Rj` {two word instruction}

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
