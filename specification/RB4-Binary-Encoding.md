# RB4 Binary Instruction Encoding

**Version:** 0.2.0
**Status:** Draft

## 1. Overview

RB4 uses logical instruction units of 8 bits.

The instruction encoding is independent of
the physical memory profile.

RB4-N and RB4-B must interpret the same
logical instruction stream identically.

Instructions may have variable lengths.

## 2. Logical Instruction Unit

The logical instruction unit is 8 bits wide.

It is the basic unit used for instruction encoding,
instruction decoding and PC progression.

The PC identifies logical instruction positions.

Physical memory organization does not change
the logical instruction unit.

## 3. Basic Instruction Format

The proposed basic instruction format is:

| Field | Width |
|---|---:|
| Opcode | 4 bits |
| Operand | 4 bits |
| Total | 8 bits |

The opcode identifies the main instruction
or instruction group.

The operand field contains register selectors,
small immediate values or suboperation information.

## 4. Opcode Groups

The architecture uses a 4-bit main opcode field.

This provides 16 possible main opcode values.

To support more instructions than the main opcode
field can directly represent, grouped opcodes
may use additional subopcode fields.

A grouped instruction may interpret part or all
of its operand field as a subopcode.

The exact allocation must be defined in the
final opcode table.

## 5. Register Encoding

RB4 provides two general-purpose registers:

| Register | Proposed Encoding |
|---|---|
| R0 | 0 |
| R1 | 1 |

Other register selector values are reserved
unless explicitly assigned by the final encoding.

Invalid register selectors cause FAULT.

## 6. Immediate Values

RB4 supports short and extended immediate values.

### 6.1 Short Immediate

A short immediate uses the 4-bit operand field.

Its unsigned range is:

0 to 15.

### 6.2 Extended Immediate

An extended immediate uses an additional
8-bit logical instruction unit.

The extended value is interpreted according
to the instruction definition.

For LDI, the destination register remains 4 bits.

Therefore, an extended immediate does not
increase the width of the destination register.

The value must still be representable in
the destination register unless a future
instruction explicitly defines another behavior.

## 7. Extended Instructions

An instruction may contain additional 8-bit
logical instruction units.

These units may encode:

- Immediate values.
- Absolute addresses.
- Relative offsets.
- Extended operands.

The instruction length is determined by
the opcode and its assigned format.

The PC advances by the complete instruction length.

## 8. Absolute Branch Encoding

Absolute branch instructions use an explicit
8-bit logical instruction address.

The target range is:

0x00 to 0xFF.

The target identifies a logical instruction position.

## 9. Relative Branch Encoding

Relative branches use signed offsets.

Offsets are represented using two's complement.

The offset is added to the logical address
immediately following the branch instruction.

PC arithmetic wraps modulo 256.

The exact offset width and encoding form
must be specified in the final opcode table.

## 10. Instruction Length

RB4 supports variable-length instructions.

An instruction may occupy:

- One logical 8-bit unit.
- Two logical 8-bit units.
- Additional units if explicitly defined
  by a future compatible extension.

Every instruction format must define
its exact length.

The decoder must know the complete instruction
length before executing the instruction.

## 11. Reserved Encodings

Unused opcode and subopcode values are reserved.

Reserved values must not be assigned conflicting
meanings by independent compatible implementations.

Future extensions may use reserved encodings
when their behavior is formally documented.

## 12. Invalid Instructions

The following conditions cause FAULT:

- Undefined main opcode.
- Undefined subopcode.
- Invalid register selector.
- Invalid operand combination.
- Invalid instruction format.
- Incomplete extended instruction at the end
  of the logical instruction address space.

The processor must not execute an incomplete
extended instruction.

## 13. Instruction Fetch

Instruction fetching operates on logical
8-bit instruction units.

The implementation must retrieve the complete
logical instruction unit regardless of
the physical memory profile.

In RB4-N, each logical unit is assembled
from two physical 4-bit positions.

In RB4-B, each logical unit is obtained
from one physical 8-bit position.

## 14. Encoding Compatibility

The binary meaning of an instruction must
remain stable after specification freeze.

Any incompatible encoding change requires
a new architecture revision.

Compatible implementations must interpret
the same binary program identically.

## 15. Final Opcode Allocation

The following information remains to be finalized:

- Main opcode allocation.
- Subopcode allocation.
- Operand field interpretation.
- Extended instruction formats.
- Exact instruction lengths.
- Relative offset widths.
- Reserved encoding ranges.

These assignments must be published
before the RB4 binary encoding is frozen.
