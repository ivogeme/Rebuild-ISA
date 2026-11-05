# Rebuild ISA

**An Open Instruction Set Architecture Designed for Reconstructability**

Rebuild ISA is an experimental open instruction set architecture
focused on simplicity, reproducibility, hardware independence and
long-term technological sustainability.

The project explores how computing architectures can be designed
to remain implementable across different levels of technological
complexity.

Its central goal is to create an architecture that can be understood,
implemented and reconstructed using different hardware technologies,
including simple digital logic and discrete electronic components.

## Project Goals

- Create a simple and open instruction set architecture.
- Allow independent hardware implementations.
- Minimize mandatory hardware complexity.
- Provide complete and accessible technical documentation.
- Support software emulation and hardware experimentation.
- Establish a foundation for future architectural extensions.
- Preserve architectural compatibility across different implementations.
- Encourage long-term hardware and software reproducibility.

## Architecture Family

The initial architecture is RB4, a minimal 4-bit processor
architecture.

Future members of the family may include:

- RB8
- RB16
- RB32

These architectures will follow the project's principles of
simplicity and reconstructability.

Each architecture may introduce different capabilities while
maintaining the fundamental design philosophy of Rebuild ISA.

## RB4

RB4 is the first architecture under development.

Its architectural design includes:

- 4-bit general-purpose registers.
- Two general-purpose registers: R0 and R1.
- 8-bit logical Program Counter.
- 2-bit status register.
- 8-bit logical instruction units.
- Variable-length instructions.
- Two memory profiles: RB4-N and RB4-B.
- Absolute and relative jumps.
- Unified code and data memory.
- Memory-mapped input/output.
- Defined arithmetic and logical operations.
- Architectural RUNNING, HALTED and FAULT states.
- Mandatory compatibility and conformance rules.

The base architecture is designed to avoid unnecessary hardware
complexity.

Features such as multiplication, division, floating-point operations,
cache memory, memory management units and hardware stack pointers
are not mandatory.

## Memory Profiles

RB4 defines two physical memory profiles.

### RB4-N

Memory organized into 4-bit addressable physical units.

The profile uses 512 physical positions, providing 2048 bits
of total memory capacity.

Each logical 8-bit instruction unit occupies two consecutive
physical 4-bit positions.

### RB4-B

Memory organized into 8-bit addressable physical units.

The profile uses 256 physical positions, providing 2048 bits
of total memory capacity.

Each logical 8-bit instruction unit occupies one physical
8-bit position.

Each physical position can also store two logical 4-bit values.

Both profiles are designed to implement the same logical
instruction encoding and architectural behavior.

The physical memory organization must not change the meaning
of RB4 programs.

## Instruction Set

The proposed base instruction set includes:

### Control

- NOP
- HALT

### Data Transfer

- LDI
- MOV
- LOAD
- STORE

### Arithmetic

- ADD
- SUB
- INC
- DEC

### Logical

- AND
- OR
- XOR
- NOT

### Branching

- JMP
- JZ
- JNZ

The instruction set uses 4-bit register values and supports
short and extended instruction forms.

The complete instruction encoding is documented separately.

## Architectural Registers

RB4 provides:

- R0: 4-bit general-purpose register.
- R1: 4-bit general-purpose register.
- PC: 8-bit logical instruction address.
- FLAGS: 2-bit status register.

The FLAGS register contains:

- Z: Zero flag.
- C: Carry/Borrow flag.

The PC uses modular 8-bit arithmetic.

## Memory and Input/Output

RB4 uses a unified memory architecture.

Instructions and data share the same physical memory.

Self-modifying code is permitted.

Memory-mapped input/output is part of the architecture.

The exact peripheral address map may be defined by an
implementation or a future standard extension.

## Development Status

**Current status: Architecture specification in development.**

The fundamental architectural direction and major design
decisions have been established.

The following areas are being formalized:

- Logical and physical memory addressing.
- Memory mapping for RB4-N and RB4-B.
- Binary instruction encoding.
- Opcode and subopcode allocation.
- Instruction execution semantics.
- Architectural fault behavior.
- Implementation requirements.
- Mandatory conformance tests.

The architectural specification is not yet frozen.

## Documentation

The specification is organized into separate documents:

- `RB4-Architecture.md`
- `RB4-Memory-Model.md`
- `RB4-Registers.md`
- `RB4-Instruction-Set.md`
- `RB4-Binary-Encoding.md`
- `RB4-Execution-Model.md`
- `RB4-Addressing-Model.md`
- `RB4-Conformance-Tests.md`
- `RB4-Implementation-Requirements.md`

## License

Copyright © 2026 Ivo Luis Miranda dos Santos

This source-code documentation is licensed under the
CERN Open Hardware Licence Version 2 - Strongly Reciprocal
(CERN-OHL-S-2.0).

You may redistribute and modify this documentation under
the terms of the CERN-OHL-S-2.0.

https://ohwr.org
