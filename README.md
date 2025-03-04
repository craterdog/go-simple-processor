<img src="https://craterdog.com/images/CraterDog.png" width="50%">

## Go Simple Processor Simulator

### Overview
This project defines the design and instruction set for a simple 16 bit parallel
processor whose purpose is to handle the processing of one request from an
external source and resulting in one response back to the external source.

![Processor Flow](https://raw.githubusercontent.com/wiki/craterdog/go-simple-processor/docs/images/Processor%20Flow.jpg)

It is ideal for the latest cloud-based web-service architectures like AWS Lambda
Functions or Azure Functions that execute a function for each web request that is
received.  These architectures don't require the overhead of an operating system
running on a traditional microprocessor.

This project also provides a Go based simulator for the simple processor.

### Architecture
This simple processor architecture is made up of two major components—the
Central Processing Unit (CPU) and the Memory Unit, both using 16 bit words and
memory addressing.

![Architecture Model](https://raw.githubusercontent.com/wiki/craterdog/go-simple-processor/docs/images/Processor%20Architecture.jpg)

#### Registers
 * `Rn` {general purpose registers: `R1` - `R4`}
 * `PC` {program counter containing the address of the current instruction}
 * `IR` {instruction register containing the instruction being executed}
 * `DA` {address register containing the start of the data memory}
 * `ST` {status register containing nine system flags and seven user flags}

#### Flags
 * `Fn` {general purpose user flags: `F1` - `F7` which are setable}
 * `FC` {carry flag which is setable}
 * `FV` {overflow flag}
 * `FN` {negative result flag}
 * `FZ` {zero result flag}
 * `FB` {boot mode flag}
 * `FD` {data mode flag}
 * `FI` {I/O error flag}
 * `FA` {address error flag}
 * `FP` {permission error flag}

#### Memory
 * 64K of addressable words {`0x0000` - `0xFFFF`}
 * 1 word = 16 bits
 * Address `0x0000` is for I/O {readable and writable}
 * Addresses `0x0001` - `0x0007` are the persistent, read-only boot instructions
 * Addresses `0x0008` - `0xFFFF` are random access memory (RAM)

### Instruction Set
The instruction set for this processor is made up of four types of instructions.

#### Control Instructions
Each control instruction determines where the next instruction to be executed is
located in memory.  The location may be fixed or depend on the whether or not a
particular status flag has been set.

#### Status Flag Instructions
The status flag instructions allow a program to modify the state of the user
status flags: `F1` - `F7` and the carry flag `FC` which is used for both
arithmetic operations and bit shifting operations.

#### Arithmetic/Logic Instructions
The arithmetic and logic instructions utilize the ALU to perform operations that
may involve the values in registers and stores the result of each operation in a
register.

#### Memory Access Instructions
The memory access instructions are used to read data from, and write data to,
specific memory locations.  They are also used to read in a web request and
write out the generated response.

### Getting Started
For detailed information on this module click
[here](https://github.com/craterdog/go-simple-processor/wiki).

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
