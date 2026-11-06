# RB4 Implementation Requirements

**Version:** 0.2.0
**Status:** Draft

## 1. Overview

This document defines the implementation
requirements for RB4 processors.

It separates mandatory architectural capabilities
from optional implementation features.

RB4 is designed to support implementations
using different technologies and hardware complexity.

## 2. Mandatory Architectural Features

A compatible RB4 implementation must provide:

- Two 4-bit general-purpose registers.
- An 8-bit Program Counter.
- A 2-bit FLAGS register.
- The defined RB4 instruction set.
- The defined logical instruction encoding.
- The selected RB4 memory profile.
- Unified instruction and data memory.
- Logical 8-bit instruction fetching.
- Logical 4-bit data operations.
- Absolute and relative branching.
- Memory-mapped input/output capability.
- RUNNING, HALTED and FAULT states.
- Defined reset behavior.
- Defined arithmetic and logical behavior.

## 3. Memory Requirements

An implementation must declare whether
it supports:

- RB4-N.
- RB4-B.

The selected profile must preserve
the logical memory behavior defined
by the architecture.

Both profiles provide 2048 bits
of total memory capacity.

The physical memory organization may differ.

## 4. Register Requirements

The implementation must provide the
architectural registers:

- R0.
- R1.
- PC.
- FLAGS.

Additional internal registers are permitted.

The implementation must not expose
additional architectural registers unless
they are defined by a compatible extension.

## 5. Execution Requirements

The implementation must correctly perform:

- Instruction fetching.
- Instruction decoding.
- Instruction execution.
- Architectural register updates.
- Memory access.
- Branch handling.
- HALT handling.
- FAULT handling.
- Reset.

The architecture does not prescribe
a specific internal execution sequence.

## 6. Optional Hardware Features

The following features are optional:

- Hardware multiplication.
- Hardware division.
- Floating-point operations.
- Cache memory.
- Memory management units.
- Hardware stack pointer.
- Interrupt controller.
- Hardware privilege levels.
- Memory protection.
- Instruction prefetch.
- Pipelining.
- DMA.
- Additional peripherals.

Optional features must not change
the behavior of the base architecture.

## 7. Internal Implementation Freedom

Implementations may differ in:

- Logic gate selection.
- Circuit topology.
- Clock frequency.
- Memory technology.
- Control unit design.
- Internal register arrangement.
- Instruction decoder organization.
- Number of internal execution stages.
- Reset circuitry.

The only requirement is preservation
of the defined architectural behavior.

## 8. Hardware Technology Independence

RB4 is designed to be implementable
using different hardware technologies.

Possible implementations include:

- Discrete logic.
- TTL integrated circuits.
- CMOS integrated circuits.
- Programmable logic devices.
- FPGA devices.
- Custom integrated circuits.
- Software emulation.

No specific semiconductor process
is required.

## 9. Clocking

The architecture does not mandate
a specific clock frequency.

An implementation may use any clock
frequency compatible with its hardware design.

The number of clock cycles required
per instruction is implementation-dependent.

## 10. Input/Output

Implementations must provide the
architectural MMIO mechanism.

The actual peripherals are implementation-dependent.

Possible peripherals include:

- LEDs.
- Buttons.
- Displays.
- Keyboards.
- Serial interfaces.
- Timers.

The base architecture does not require
a specific peripheral set.

## 11. Compatibility Requirements

An implementation must preserve:

- Register behavior.
- Instruction semantics.
- Memory behavior.
- Branching behavior.
- Reset behavior.
- HALT behavior.
- FAULT behavior.

Internal hardware differences must not
alter architectural results.

## 12. Conformance

Compatible implementations must pass
the mandatory tests defined in
`RB4-Conformance-Tests.md`.

An implementation that supports only
a subset of the architecture must
identify itself as a partial implementation.

## 13. Extensions

Future extensions may add new capabilities.

Extensions must be documented separately.

They must not silently change the meaning
of existing instructions or architectural registers.

Incompatible extensions must be identified
as incompatible with the base RB4 architecture.

## 14. Ratified Addressing and Memory Requirements

Implementations must use 9-bit logical data addresses
covering 0x000–0x1FF.

RB4-B must map selector 0 to the lower nibble and selector 1
to the upper nibble.

A partial-nibble write must preserve the other nibble.

RB4-N must assemble each logical instruction unit with
the first physical nibble as the low-order nibble and the
second physical nibble as the high-order nibble.

Logical data addresses 0x1F0–0x1FF are reserved for MMIO.

LOAD and STORE accesses to these addresses must be routed
to the MMIO interface.

Instruction fetch must continue to read unified program
memory normally.

An access to an MMIO register not implemented by the selected
platform profile must cause FAULT.
