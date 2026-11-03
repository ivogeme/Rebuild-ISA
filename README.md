# Rebuild ISA

**An Open Instruction Set Architecture Designed for Reconstructability**

Rebuild ISA is an experimental open instruction set architecture
focused on simplicity, reproducibility, hardware independence and
long-term technological sustainability.

The project explores how computing architectures can be designed
to remain implementable across different levels of technological
complexity.

## Project Goals

- Create a simple and open instruction set architecture.
- Allow independent hardware implementations.
- Minimize mandatory hardware complexity.
- Provide complete and accessible technical documentation.
- Support software emulation and hardware experimentation.
- Establish a foundation for future architectural extensions.

## Architecture Family

The initial architecture is RB4, a minimal 4-bit processor
architecture.

Future members of the family may include:

- RB8
- RB16
- RB32

These architectures will follow the project's principles of
simplicity and reconstructability.

## RB4

RB4 is the first architecture under development.

Its initial design includes:

- 4-bit general-purpose registers.
- Two general-purpose registers: R0 and R1.
- 8-bit program counter.
- 2-bit status register.
- 8-bit logical instruction units.
- Two memory profiles: RB4-N and RB4-B.
- Absolute and relative jumps.
- Unified code and data memory.
- Memory-mapped input/output.

## Memory Profiles

### RB4-N
Memory organized into 4-bit addressable units.

### RB4-B
Memory organized into 8-bit addressable units.

Both profiles are designed to share the same logical instruction
encoding.

## Development Status

**Current status: Architecture specification in development.**

The architectural specification is not yet frozen.
Instruction encoding, opcode allocation and implementation
details are still being defined.

## License

Copyright © 2026 Ivo Luis Miranda dos Santos

Esta documentação de código-fonte está licenciada sob a CERN Open Hardware Licence 
Version 2 - Strongly Reciprocal (CERN-OHL-S-2.0).

Você pode redistribuir e modificar esta documentação sob os termos da 
CERN-OHL-S-2.0 (https://ohwr.org).
