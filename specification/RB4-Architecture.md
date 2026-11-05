# RB4 Architecture Specification

**Architecture:** Rebuild ISA RB4  
**Specification Version:** 0.2.0  
**Status:** Draft

## 1. Overview

RB4 is a minimal 4-bit processor architecture developed
as part of the Rebuild ISA project.

Its primary design goals are simplicity, reproducibility,
hardware independence and long-term technological sustainability.

The architecture is intended to support implementations
using different digital technologies, including discrete
logic, TTL, CMOS and programmable logic devices.

RB4 separates its logical architectural behavior from
the physical hardware used to implement it.

This allows different implementations to execute the
same programs while using different internal designs.

## 2. Architectural Characteristics

| Feature | Specification |
|---|---|
| Data width | 4 bits |
| General-purpose registers | 2 |
| Register names | R0, R1 |
| Program counter | 8 bits |
| Status register | 2 bits |
| Logical instruction unit | 8 bits |
| Logical instruction address width | 8 bits |
| Memory profiles | RB4-N and RB4-B |
| Organization | RISC |
| Memory model | Unified |
| Instruction length | Variable |
| Arithmetic representation | 4-bit values |
| Input/output model | Memory-mapped I/O |

The logical instruction unit is always 8 bits,
independently of the physical memory profile.

## 3. General-Purpose Registers

RB4 provides two general-purpose registers:

- R0
- R1

Each register stores a 4-bit value.

The unsigned representable range is:

0 to 15.

The signed representable range using two's complement is:

-8 to +7.

Both registers support arithmetic, logical and
data-transfer operations as defined by the ISA.

There is no mandatory zero register.

## 4. Program Counter

The Program Counter (PC) is an 8-bit architectural register.

It identifies the logical address of the next instruction.

The logical instruction address space contains
256 positions, from 0x00 to 0xFF.

PC arithmetic uses modular 8-bit behavior.

An increment beyond 0xFF wraps to 0x00.

The PC always operates on logical instruction addresses.

Its architectural behavior is independent of the
physical memory organization used by RB4-N or RB4-B.

Physical memory mapping is defined separately.

## 5. Status Register

The FLAGS register contains two bits:

- Z: Zero flag.
- C: Carry/Borrow flag.

Bit allocation:

| Bit | Name | Description |
|---|---|---|
| 0 | Z | Zero |
| 1 | C | Carry/Borrow |

The exact update behavior is defined in the
instruction set specification.

## 6. Instruction Architecture

RB4 uses logical instruction units of 8 bits.

The basic instruction format is designed around:

- 4-bit opcode field.
- 4-bit operand field.

Instructions may use additional 8-bit logical units
when extended values are required.

The architecture supports variable-length instructions.

The complete binary encoding is defined in
`RB4-Binary-Encoding.md`.

## 7. Memory Architecture

RB4 uses a unified memory model.

Instructions and data share the same physical memory.

The architecture defines two physical memory profiles:

### RB4-N

- 4-bit physical addressable units.
- 512 physical positions.
- 2048 bits of total storage.
- Each logical instruction unit occupies two
  consecutive physical positions.

### RB4-B

- 8-bit physical addressable units.
- 256 physical positions.
- 2048 bits of total storage.
- Each logical instruction unit occupies one
  physical position.

Both profiles provide the same total storage capacity
and must implement the same logical instruction behavior.

The exact relationship between logical instruction
addresses, logical data addresses and physical memory
positions is defined in the addressing specification.

## 8. Input/Output Architecture

RB4 supports memory-mapped input/output.

External devices may be accessed through memory
addresses assigned to I/O functions.

Possible devices include:

- LEDs.
- Buttons.
- Displays.
- Keyboards.
- Serial interfaces.
- Timers.

The architecture requires the MMIO mechanism.

The exact peripheral address map is not mandatory
for the base architecture and may be defined by
an implementation or a future standard extension.

## 9. Architectural Limitations

The base RB4 architecture does not require:

- Hardware multiplication.
- Hardware division.
- Floating-point operations.
- Memory management units.
- Cache memory.
- Hardware stack pointers.
- Interrupt controllers.
- Hardware privilege levels.
- Mandatory memory protection.

Implementations may provide additional internal
hardware as long as the architectural behavior
remains compatible.

## 10. Execution States

RB4 defines three architectural execution states:

- RUNNING
- HALTED
- FAULT

The processor starts in RUNNING state after reset.

HALT places the processor in the HALTED state.

Invalid instructions and invalid instruction formats
cause the processor to enter FAULT.

The detailed behavior is defined in
`RB4-Execution-Model.md`.

## 11. Compatibility

All compatible RB4 implementations must preserve
the architectural behavior defined by this specification.

Different implementations may use different:

- Logic circuits.
- Internal registers.
- Memory technologies.
- Clock frequencies.
- Control unit designs.
- Instruction decoding methods.

However, they must produce the same architectural
results for the same valid program and initial state.

The physical memory profile must not change
the logical meaning of an instruction.

## 12. Specification Stability

Existing instruction behavior must not be silently
changed after the specification is finalized.

Future extensions must be explicitly documented.

Reserved encodings must remain available for
future compatible extensions where applicable.

Any incompatible architectural change must be
identified as a new architecture revision or family member.
