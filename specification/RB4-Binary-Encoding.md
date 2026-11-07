# RB4 Binary Instruction Encoding

**Version:** 0.3.0
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

LDI is the only base instruction that loads
an immediate value into a general-purpose register.

LDI always occupies exactly two logical
instruction units.

The first unit contains the opcode and
destination register selector.

The second unit contains the immediate value.

The valid immediate range is 0 through 15.

No short one-unit LDI form exists.

An immediate value outside this range causes FAULT.
Implementations must not truncate or wrap
the immediate value.

### 6.1 LDI Encoding

First unit:

0001 R000

Second unit:

0000 IIII

R selects the destination register.

IIII represents the unsigned 4-bit immediate.

Examples:

LDI R0, 5: 10 05
LDI R1, 15: 18 0F

### 6.2 LDI Encoding Restrictions

LDI always uses exactly two logical instruction units.

The second unit must have its upper four bits set to zero.

The lower four bits contain the immediate value,
which must be in the range 0 through 15.

Any LDI encoding that violates this format causes FAULT.

No short or extended LDI alternative encoding exists.


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

## 15. Canonical Instruction Encoding

The following encoding is normative for
the RB4 base instruction set.

All instructions use one, two or three
logical 8-bit instruction units.

The first unit determines the instruction
format and total instruction length.

No alternative encoding is permitted
for an already defined base instruction.

### 15.1 Control

NOP:
0000 0000

HALT:
0000 0001

All other Control encodings are invalid.

### 15.2 LDI

First unit:
0001 R000

Second unit:
0000 IIII

### 15.3 MOV and Register Operations

MOV, ADD, SUB, AND and OR:

OOOO D S 00

D selects the destination register.
S selects the source register.

### 15.4 INC and DEC

0110 O R 00

O = 0: INC
O = 1: DEC

R selects the register.

### 15.5 XOR and NOT

XOR:

1001 0 D S 0

NOT:

1001 1 R 00

### 15.6 LOAD and STORE

First unit:

0011 W R 00

Second unit:

A7 A6 A5 A4 A3 A2 A1 A0

Third unit:

0000000 A8

W = 0: LOAD
W = 1: STORE

R selects the register.

### 15.7 Absolute JMP

First unit:

1010 0000

Second unit:

AAAAAAAA

### 15.8 Conditional Absolute Jumps

First unit:

1011 C000

Second unit:

AAAAAAAA

C = 0: JZ
C = 1: JNZ

### 15.9 Relative Jumps

First unit:

1100 CC00

Second unit:

SSSSSSSS

CC = 00: unconditional relative jump
CC = 01: relative JZ
CC = 10: relative JNZ
CC = 11: invalid

The offset is an 8-bit signed two's
complement value.

## 16. Ratified Main Opcode Groups

The following main opcode allocation is approved for the RB4 base
encoding. Each value is the 4-bit opcode field.

| Opcode | Group |
|---|---|
| 0000 | Control |
| 0001 | Immediate load (LDI) |
| 0010 | Register transfer (MOV) |
| 0011 | Memory operations (LOAD, STORE) |
| 0100 | Addition (ADD) |
| 0101 | Subtraction (SUB) |
| 0110 | Increment and decrement (INC, DEC) |
| 0111 | AND |
| 1000 | OR |
| 1001 | XOR and NOT |
| 1010 | Absolute unconditional jump (JMP) |
| 1011 | Conditional absolute jumps (JZ, JNZ) |
| 1100 | Relative jumps |
| 1101 | Extended operations |
| 1110 | Reserved |
| 1111 | Reserved |

The allocation above fixes the main opcode groups. It does not,
by itself, finalize the subopcode values or the bit-level operand
layout inside each group. Those details must be specified before
the binary encoding can be frozen.

## 17. Extended Immediate Range

For LDI, both short and extended immediate forms must represent
a value from 0 through 15. An extended LDI immediate greater than
15 causes FAULT. Implementations must not silently truncate the
value or wrap it modulo 16.

## 18. Relative PC Boundary Rule

PC arithmetic is modular over 8 bits. A complete instruction
whose final logical unit is at address 0xFF may advance the PC
to 0x00.

An instruction beginning at 0xFF that requires one or more
additional logical instruction units is incomplete and causes
FAULT. Instruction fetch for an incomplete instruction does not
wrap around to address 0x00 to complete that instruction.

## 19. Logical Data Address Width

Logical data addresses are 9 bits wide and identify one of
512 logical 4-bit positions, from 0x000 through 0x1FF.
The physical memory profile must not change this logical address
width or its meaning.

## 20. MMIO Data-Address Window

The logical data-address range 0x1F0 through 0x1FF is reserved
for memory-mapped input/output access. LOAD and STORE accesses
to this range are routed to the MMIO interface rather than
ordinary RAM.

Instruction fetch continues to use the unified program memory
and is not redirected by this data-access MMIO window.
This permits instruction storage at every logical instruction
address, including 0xF8 through 0xFF.

The device-register assignment within the MMIO window is
implementation- or platform-profile-defined. An access to an
unimplemented MMIO register causes FAULT. A common peripheral
register map remains a future standardization task.
