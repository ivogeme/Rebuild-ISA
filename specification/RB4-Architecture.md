# RB4 Architecture Specification

**Architecture:** Rebuild ISA RB4  
**Specification Version:** 0.1.0  
**Status:** Draft

## 1. Overview

RB4 is a minimal 4-bit processor architecture developed
as part of the Rebuild ISA project.

Its primary design goals are simplicity, reproducibility
and implementation independence.

The architecture is intended to support implementations
using different digital technologies, including discrete
logic, TTL, CMOS and programmable logic devices.

## 2. Architectural Characteristics

| Feature | Specification |
|---|---|
| Data width | 4 bits |
| General-purpose registers | 2 |
| Program counter | 8 bits |
| Status register | 2 bits |
| Logical instruction unit | 8 bits |
| Address width | 8 bits |
| Memory profiles | RB4-N and RB4-B |
| Organization | RISC |
| Memory model | Unified |

## 3. General-Purpose Registers

RB4 provides two general-purpose registers:

- R0
- R1

Each register stores an unsigned 4-bit value.

The representable unsigned range is 0 to 15.

Signed values may be represented using two's complement.

## 4. Program Counter

The Program Counter (PC) is an 8-bit register.

It identifies the logical address of the next instruction.

The address space contains 256 logical positions.

PC arithmetic uses modular 8-bit behavior.

## 5. Status Register

The FLAGS register contains two bits:

- Z: Zero flag.
- C: Carry/Borrow flag.

The exact update behavior is defined in the
instruction set specification.

## 6. Architectural Limitations

The base RB4 architecture does not require:

- Hardware multiplication.
- Hardware division.
- Floating-point operations.
- Memory management units.
- Cache memory.
- Hardware stack pointers.
- Interrupt controllers.

## 7. Compatibility

Future Rebuild ISA architectures may introduce
additional capabilities.

Existing instruction behavior must not be silently
changed after a specification is finalized.
