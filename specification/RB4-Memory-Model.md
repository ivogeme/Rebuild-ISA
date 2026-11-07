# RB4 Memory Model

**Version:** 0.3.0
**Status:** Draft

## 1. Overview

RB4 uses a unified memory model.

Instructions and data share the same physical memory.

The architecture defines two physical memory profiles:

- RB4-N: 4-bit physical memory organization.
- RB4-B: 8-bit physical memory organization.

Both profiles are designed to provide the same total
storage capacity and execute the same logical programs.

The logical instruction unit is always 8 bits.

The physical organization must not change the
architectural behavior of RB4.

## 2. Memory Capacity

Both memory profiles provide a total storage capacity
of 2048 bits.

This corresponds to:

- 256 bytes.
- 512 logical 4-bit values.
- 256 logical 8-bit instruction units.

The number of physical positions depends on
the selected memory profile.

## 3. RB4-N: Nibble Memory

RB4-N uses 4-bit addressable physical memory units.

Characteristics:

- 512 physical positions.
- 4 bits per position.
- 2048 bits of total storage.
- 256 bytes of equivalent capacity.

Each physical position stores one 4-bit value.

Each logical 8-bit instruction unit occupies
two consecutive physical 4-bit positions.

The physical memory organization is therefore
based on individual nibbles.

## 4. RB4-B: Byte Memory

RB4-B uses 8-bit addressable physical memory units.

Characteristics:

- 256 physical positions.
- 8 bits per position.
- 2048 bits of total storage.
- 256 bytes of capacity.

Each physical position stores one 8-bit value.

Each logical 8-bit instruction unit occupies
one physical memory position.

A physical 8-bit position can also store
two logical 4-bit values.

The implementation must define the nibble ordering
used when accessing the two logical 4-bit values
inside one physical position.

The nibble ordering must be consistent throughout
the implementation.

## 5. Logical Instruction Units

The logical instruction unit is always 8 bits.

This rule applies to both RB4-N and RB4-B.

In RB4-N:

- One logical instruction unit occupies two
  consecutive physical 4-bit positions.

In RB4-B:

- One logical instruction unit occupies one
  physical 8-bit position.

Instruction fetching must produce the same
logical 8-bit value in both memory profiles.

## 6. Logical Data Values

RB4 general-purpose registers store 4-bit values.

LOAD and STORE transfer one logical 4-bit value.

The physical implementation may need to perform
different operations depending on the memory profile.

In RB4-N, a logical 4-bit value corresponds directly
to one physical memory position.

In RB4-B, a logical 4-bit value corresponds to
one half of a physical 8-bit position.

The implementation must preserve the other half
of the physical position when modifying only one
logical 4-bit value.

## 7. Memory Operations

The architecture provides:

- LOAD: Read a 4-bit value from memory.
- STORE: Write a 4-bit value to memory.

The initial instruction set uses absolute
memory addressing.

The exact logical data address width and its
relationship to instruction addresses are defined
in the addressing specification.

Memory operations must behave identically from
the perspective of an RB4 program, regardless
of the selected physical memory profile.

## 8. Unified Memory

Program instructions and data share the same
physical memory.

The architecture does not require separate
instruction and data memories.

Self-modifying code is permitted.

A program may modify memory locations that
contain instructions.

The effects of modifying instruction memory
must be visible to subsequent instruction fetches.

No memory protection mechanism is mandatory.

## 9. Memory Access Rules

All memory accesses must follow the logical
addressing rules defined by RB4.

Physical memory access is implementation-dependent.

An implementation may use:

- Direct memory access.
- Address decoding logic.
- Multiplexers.
- Latches.
- Separate internal read and write circuits.
- Other equivalent hardware mechanisms.

The implementation must preserve the logical
behavior defined by the architecture.

## 10. Memory-Mapped Input/Output

RB4 uses memory-mapped input/output.

External devices may be accessed through reserved
memory addresses.

Possible devices include:

- LEDs.
- Buttons.
- Displays.
- Keyboards.
- Serial interfaces.
- Timers.

The exact device address map is defined by
the implementation or a future standard extension.

The MMIO mechanism is part of the architecture.

## 11. Physical Memory Mapping

The physical mapping between logical memory
addresses and physical memory positions must
be defined independently of the instruction set.

The mapping must support both RB4-N and RB4-B.

It must also preserve:

- Unified code and data memory.
- Logical 8-bit instruction units.
- Logical 4-bit data values.
- Self-modifying code.
- Equivalent architectural behavior.

The exact mapping rules are specified in
`RB4-Addressing-Model.md`.

## 12. Compatibility

Both memory profiles must execute compatible
RB4 programs.

The same logical program must produce the same
architectural results on RB4-N and RB4-B.

Differences in physical memory organization
must remain invisible to software.

An implementation that changes the logical
behavior of memory operations is not compatible
with the RB4 specification.



## 13. Ratified Nibble Ordering

The logical nibble ordering is fixed for both memory profiles.

In RB4-N, for a logical instruction unit assembled from two
consecutive physical nibbles:

- The first physical nibble is the low-order nibble (bits 3:0).
- The second physical nibble is the high-order nibble (bits 7:4).

The logical instruction unit is therefore:

(second_nibble << 4) | first_nibble

In RB4-B, logical data address selector 0 selects the lower
nibble (bits 3:0), and selector 1 selects the upper nibble
(bits 7:4).

A STORE to one nibble must preserve the other nibble exactly.

## 14. MMIO Data-Access Overlay

Logical data addresses 0x1F0 through 0x1FF are reserved
for MMIO. LOAD and STORE operations to these addresses
are routed to the MMIO interface instead of ordinary RAM.

This reservation applies to logical data accesses. Instruction
fetch continues to read the unified program memory at the
logical instruction address, including the final instruction
addresses.

The physical memory capacity remains 2048 bits;
the MMIO window does not change the physical storage size.

The mapping of individual devices and registers inside
the MMIO window is defined by a platform profile. Access to
an unimplemented MMIO register causes FAULT.

## 15. Canonical Logical Memory Mapping

RB4 defines 512 logical 4-bit data positions.

Logical data addresses range from 0x000
through 0x1FF.

For RB4-N:

PhysicalAddress = LogicalDataAddress

For RB4-B:

PhysicalAddress = LogicalDataAddress >> 1

NibbleSelector = LogicalDataAddress & 1

Selector 0 accesses bits 3:0.

Selector 1 accesses bits 7:4.

A partial-nibble STORE must preserve
the other nibble in the physical byte.

### 15.1 Instruction Fetch

For logical instruction address A:

RB4-N:

LowNibble = Memory[2 * A]
HighNibble = Memory[2 * A + 1]

LogicalByte = (HighNibble << 4) | LowNibble

RB4-B:

LogicalByte = Memory[A]

Instruction fetching always reads
the unified program memory.

The MMIO data-address overlay does not
redirect instruction fetching.



