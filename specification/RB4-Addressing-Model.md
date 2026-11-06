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
underlying memory contents.

A STORE operation may therefore modify
part of a logical instruction unit.

Subsequent instruction fetching must observe
the modified memory contents.

## 6. RB4-N Physical Mapping

RB4-N uses 4-bit physical memory positions.

The physical memory contains 512 positions.

Each logical 4-bit address maps directly
to one physical position.

Proposed mapping:

PhysicalAddress = LogicalDataAddress

For instruction fetching, logical instruction
address A maps to two consecutive physical
positions:

- Position 2 × A.
- Position 2 × A + 1.

The two positions are combined to produce
one logical 8-bit instruction unit.

## 7. RB4-B Physical Mapping

RB4-B uses 8-bit physical memory positions.

The physical memory contains 256 positions.

Each physical position stores two logical
4-bit values.

A logical data address identifies:

- One physical 8-bit position.
- One 4-bit half of that position.

Proposed mapping:

PhysicalAddress = LogicalDataAddress / 2

NibbleSelector = LogicalDataAddress modulo 2

The implementation must use a consistent
nibble ordering.

The proposed ordering is:

- Selector 0: lower 4 bits.
- Selector 1: upper 4 bits.

This ordering remains subject to final
specification approval.

## 8. Instruction Fetch in RB4-B

A logical instruction address directly
identifies one physical 8-bit position.

The complete 8-bit value is retrieved
as one logical instruction unit.

The two 4-bit halves may also be accessed
individually through logical data addressing.

## 9. Instruction Fetch in RB4-N

A logical instruction address identifies
two consecutive physical 4-bit positions.

The implementation combines both positions
to construct the logical 8-bit instruction unit.

The ordering must be consistent with
the logical instruction encoding.

## 10. Memory Access

LOAD reads one logical 4-bit position.

STORE writes one logical 4-bit position.

Both instructions use logical data addresses.

The physical implementation translates
the logical address into the corresponding
physical memory location.

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
to the same underlying logical memory.

The architecture does not define separate
instruction and data storage.

Self-modifying code is permitted.

All memory modifications must be visible
to subsequent instruction fetches.

## 13. Memory-Mapped Input/Output

MMIO uses logical memory addressing.

The implementation may assign logical
memory addresses to external devices.

The exact device map is implementation-defined
or may be specified by a future standard extension.

## 14. Addressing Compatibility

RB4-N and RB4-B must preserve the same
logical memory behavior.

The physical address representation
must not be visible to software.

Compatible implementations must produce
the same results for equivalent memory operations.

## 15. Remaining Specification Decisions

The following details must be finalized
before the addressing model is frozen:

- Confirmation of the 9-bit logical data address.
- Confirmation of the nibble ordering.
- Exact LOAD and STORE address encoding.
- Exact MMIO address allocation.
- Behavior of memory accesses to reserved
  or unmapped regions.
- Exact interaction between instruction
  fetch and data writes during execution.


  ## 16. Ratified Addressing Decisions

The logical data address width is confirmed as 9 bits.
Addresses range from 0x000 through 0x1FF and identify
individual logical 4-bit values.

For RB4-B, the nibble ordering is fixed as follows:

- Nibble selector 0 selects the lower four bits (bits 3:0).
- Nibble selector 1 selects the upper four bits (bits 7:4).

For a physical byte 0xB4, logical data address selector 0
reads 0x4 and selector 1 reads 0xB.

The same logical ordering applies when assembling instruction
units in RB4-N: the first physical nibble is the low-order
nibble (bits 3:0) and the second physical nibble is the
high-order nibble (bits 7:4).

Thus:

LogicalByte = (HighNibble << 4) | LowNibble

## 17. MMIO Data-Address Window

Logical data addresses 0x1F0 through 0x1FF are reserved
for MMIO. LOAD and STORE operations targeting these addresses
access the MMIO interface, not ordinary RAM.

Instruction fetch remains directed to unified program memory.
The MMIO data-address window therefore does not remove or
renumber logical instruction addresses.

The assignment of individual device registers within this
16-nibble window is platform-profile-defined. Access to an
unimplemented register causes FAULT.

## 18. Remaining Address-Encoding Work

The 9-bit logical data address width, RB4-B nibble ordering,
and RB4-N instruction nibble ordering are now fixed.

The exact binary representation of a 9-bit address in LOAD
and STORE instructions remains to be specified in the binary
encoding table.

The behavior of any memory access outside the defined logical
data-address range is invalid and causes FAULT. No such
address is representable by a conforming 9-bit address field.
