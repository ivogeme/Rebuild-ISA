# RB4 Addressing Model

**Version:** 0.2.0
**Status:** Draft

## 1. Overview

This document defines the logical and physical
addressing model of RB4.

It describes how logical instruction addresses
and logical data addresses relate to the
physical memory organization.

RB4 defines two physical memory profiles:

- RB4-N.
- RB4-B.

Both profiles must preserve identical
architectural behavior.

## 2. Logical Memory Organization

RB4 defines a logical memory capacity
of 2048 bits.

This capacity is equivalent to:

- 256 logical 8-bit units.
- 512 logical 4-bit positions.
- 256 bytes.

The architecture uses logical 8-bit units
for instruction encoding.

Logical 4-bit positions are used for
general-purpose data values.

## 3. Instruction Addressing

The Program Counter is an 8-bit register.

It identifies logical 8-bit instruction units.

The instruction address range is:

0x00 to 0xFF.

Each instruction address identifies
one logical 8-bit memory unit.

The PC does not directly identify individual
4-bit data positions.

## 4. Logical Data Addressing

The logical memory contains 512 positions
of 4 bits each.

A logical data address must therefore
identify one of 512 positions.

The logical data address requires
9 bits of information.

The logical data address range is:

0x000 to 0x1FF.

Each address identifies one logical
4-bit value.

## 5. Relationship Between Instruction
## and Data Addresses

Each logical 8-bit instruction unit
contains two logical 4-bit positions.

For logical instruction address A:

- The first 4-bit position corresponds
  to logical data position 2 × A.
- The second 4-bit position corresponds
  to logical data position 2 × A + 1.

This relationship ensures that instruction
and data accesses refer to the same
underlying physical memory contents.

A STORE operation may modify part of a
logical instruction unit when its target
logical data address is not reserved for MMIO.

Logical data addresses 0x1F0 through 0x1FF
are reserved for MMIO and do not provide
ordinary RAM access.

Subsequent instruction fetching must observe
modifications made to the underlying program
memory, including memory positions corresponding
to the MMIO data-address window.

## 6. RB4-N Physical Mapping

RB4-N uses 4-bit physical memory positions.

The physical memory contains 512 positions.

Each logical 4-bit address maps directly
to one physical position.

Canonical mapping:

PhysicalAddress = LogicalDataAddress

For instruction fetching, logical instruction
address A maps to two consecutive physical
positions:

- Position 2 × A.
- Position 2 × A + 1.

The first physical position contains
the low-order nibble.

The second physical position contains
the high-order nibble.

The two positions are combined to produce
one logical 8-bit instruction unit.

## 7. RB4-B Physical Mapping

RB4-B uses 8-bit physical memory positions.

The physical memory contains 256 positions.

Canonical mapping:

PhysicalAddress = LogicalDataAddress >> 1

NibbleSelector = LogicalDataAddress & 1

The nibble ordering is fixed:

- Selector 0: lower 4 bits (bits 3:0).
- Selector 1: upper 4 bits (bits 7:4).

This ordering is mandatory for all compatible
RB4-B implementations.

A STORE operation must preserve the other
nibble in the physical byte.

## 8. Instruction Fetch in RB4-B

A logical instruction address directly
identifies one physical 8-bit position.

The complete 8-bit value is retrieved
as one logical instruction unit.

The two 4-bit halves may also be accessed
individually through logical data addressing.

Instruction fetching reads the complete
physical byte and is not redirected by
the MMIO data-address window.

## 9. Instruction Fetch in RB4-N

A logical instruction address identifies
two consecutive physical 4-bit positions.

The implementation combines both positions
to construct the logical 8-bit instruction unit.

The first physical nibble is the low-order nibble.

The second physical nibble is the high-order nibble.

The resulting logical instruction unit is:

LogicalByte = (HighNibble << 4) | LowNibble

## 10. Memory Access

LOAD reads one logical 4-bit position.

STORE writes one logical 4-bit position.

Both instructions use logical data addresses.

The physical implementation translates
the logical address into the corresponding
physical memory location.

The logical data address range is:

0x000 to 0x1FF.

## 11. Partial Writes in RB4-B

When STORE modifies one logical 4-bit value
inside a physical 8-bit position, the other
4-bit value must remain unchanged.

The implementation may use:

- Read-modify-write logic.
- Separate nibble write enables.
- Other equivalent mechanisms.

The architectural result must be identical.

## 12. Unified Memory

Instruction and data addressing refer
to the same underlying physical memory.

The architecture does not define separate
instruction and data storage.

Self-modifying code is permitted.

STORE operations may modify instruction
memory through logical data addresses that
are not reserved for MMIO.

All modifications to instruction memory
must be visible to subsequent instruction fetches.

The MMIO data-address window, 0x1F0 through
0x1FF, redirects LOAD and STORE operations
to the MMIO interface instead of ordinary RAM.

Instruction fetching always uses the logical
instruction address space and is never redirected
by the MMIO data-address window.

## 13. Memory-Mapped Input/Output

MMIO uses logical data addressing.

The reserved MMIO data-address window is:

0x1F0 through 0x1FF.

LOAD and STORE operations targeting this range
access the MMIO interface instead of ordinary RAM.

The assignment of individual device registers
within this window is defined by the selected
platform profile.

Access to an unimplemented or forbidden
MMIO register causes FAULT.

Instruction fetching is not redirected by
the MMIO data-address window.

## 14. Addressing Compatibility

RB4-N and RB4-B must preserve the same
logical memory behavior.

The physical address representation
must not be visible to software.

Compatible implementations must produce
the same results for equivalent memory operations.

The selected physical memory profile must not
change the logical meaning of instruction
or data addresses.

## 15. Finalized Addressing Rules

The following addressing rules are normative:

- Logical instruction addresses are 8 bits.
- Logical instruction addresses range from 0x00 to 0xFF.
- Logical data addresses are 9 bits.
- Logical data addresses range from 0x000 to 0x1FF.
- Each logical data address identifies one 4-bit value.
- RB4-N uses two physical nibbles per logical instruction unit.
- RB4-B uses one physical byte per logical instruction unit.
- RB4-B selector 0 selects the lower nibble.
- RB4-B selector 1 selects the upper nibble.
- RB4-N assembles instruction units with the first
  physical nibble as the low-order nibble.
- LOAD and STORE use 9-bit logical data addresses.
- MMIO occupies logical data addresses 0x1F0–0x1FF.
- Instruction fetching is not redirected by MMIO.
- A partial-nibble STORE in RB4-B preserves the other nibble.

The physical representation must remain
invisible to RB4 software.

## 16. MMIO Data-Access Rules

Logical data addresses 0x1F0 through 0x1FF
are reserved for MMIO.

LOAD and STORE operations targeting these addresses
access the MMIO interface, not ordinary RAM.

This reservation applies only to logical data accesses.

Instruction fetch remains directed to unified
program memory.

The MMIO data-address window therefore does not
remove or renumber logical instruction addresses.

The assignment of individual device registers
within this 16-nibble window is platform-profile-defined.

Access to an unimplemented or forbidden
MMIO register causes FAULT.

## 17. Invalid Data Accesses

LOAD and STORE use a 9-bit logical address field.

All values from 0x000 through 0x1FF are
representable by the logical data address.

An access to an unimplemented or forbidden
MMIO register causes FAULT.

An implementation must not silently redirect
an invalid MMIO access to ordinary RAM.

The detailed fault behavior is defined in
`RB4-Execution-Model.md`.
