# RB4 Binary Instruction Encoding

**Version:** 0.3.0
**Status:** Draft

## 1. Overview

RB4 uses logical instruction units of 8 bits.

The instruction encoding is independent of
the physical memory profile.

RB4-N and RB4-B must interpret the same
logical instruction stream identically.

Instructions have variable lengths.

Every base instruction has a defined
binary encoding and instruction length.

## 2. Logical Instruction Unit

The logical instruction unit is 8 bits wide.

It is the basic unit used for instruction encoding,
instruction decoding and PC progression.

The PC identifies logical instruction positions.

Physical memory organization does not change
the logical instruction unit.

## 3. Basic Instruction Format

The RB4 basic instruction format is:

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

The main opcode allocation is defined
in Section 16.

The valid operand layouts and complete
instruction encodings are defined in Section 15.

## 5. Register Encoding

RB4 provides two general-purpose registers:

| Register | Encoding |
|---|---|
| R0 | 0 |
| R1 | 1 |

Other register selector values are reserved
unless explicitly assigned by the base encoding.

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

The second unit must have its upper four bits
set to zero.

The lower four bits contain the immediate value,
which must be in the range 0 through 15.

Any LDI encoding that violates this format
causes FAULT.

No short or extended LDI alternative encoding exists.

## 7. Extended Instructions

The base architecture defines the instruction
formats and lengths listed in Section 15.

Additional instruction formats may be introduced
only through explicitly documented extensions.

An extension must define:

- Its opcode or subopcode allocation.
- Its complete binary format.
- Its instruction length.
- Its operand interpretation.
- Its execution behavior.
- Its invalid encoding behavior.

An extension must not change the meaning
of an existing base instruction.

Reserved encodings must not be interpreted
as valid instructions by a base implementation.

## 8. Absolute Branch Encoding

Absolute branch instructions use an explicit
8-bit logical instruction address.

The target range is:

0x00 to 0xFF.

The target identifies a logical instruction position.

The target is interpreted as an absolute
logical instruction address.

## 9. Relative Branch Encoding

Relative branches use signed 8-bit offsets.

Offsets are represented using two's complement.

The offset is added to the logical address
immediately following the complete branch instruction.

The offset range is:

-128 to +127.

The target address is calculated as:

Target = (PC_after_instruction + SignExtend(offset)) mod 256

PC arithmetic wraps modulo 256.

The relative offset is stored in the second
logical instruction unit.

The first logical instruction unit identifies
the relative branch operation.

## 10. Instruction Length

RB4 supports variable-length instructions.

Base instructions occupy one, two or three
logical 8-bit instruction units.

The base instruction lengths are:

| Instruction | Length |
|---|---:|
| NOP | 1 unit |
| HALT | 1 unit |
| LDI | 2 units |
| MOV | 1 unit |
| ADD | 1 unit |
| SUB | 1 unit |
| INC | 1 unit |
| DEC | 1 unit |
| AND | 1 unit |
| OR | 1 unit |
| XOR | 1 unit |
| NOT | 1 unit |
| LOAD | 3 units |
| STORE | 3 units |
| JMP | 2 units |
| JZ | 2 units |
| JNZ | 2 units |
| JR | 2 units |
| JRZ | 2 units |
| JRNZ | 2 units |

The first unit determines the instruction
format and total instruction length.

The decoder must retrieve all required
instruction units before executing the instruction.

An incomplete multi-unit instruction at the
end of the logical instruction address space
causes FAULT.

## 11. Reserved Encodings

Unused opcode and subopcode values are reserved.

Reserved values must not be assigned conflicting
meanings by independent compatible implementations.

Future extensions may use reserved encodings
when their behavior is formally documented.

A base implementation must cause FAULT when
it encounters an encoding that is not defined
by the base instruction set.

## 12. Invalid Instructions

The following conditions cause FAULT:

- Undefined main opcode.
- Undefined subopcode.
- Invalid register selector.
- Invalid operand combination.
- Invalid instruction format.
- Invalid reserved bits.
- Incomplete multi-unit instruction at the end
  of the logical instruction address space.

The processor must not execute an incomplete
instruction.

The detailed architectural fault behavior is
defined in `RB4-Execution-Model.md`.

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

The logical instruction stream must be
identical in both physical memory profiles.

Instruction fetching is not redirected by
the MMIO data-address window.

## 14. Encoding Compatibility

The binary meaning of an instruction must
remain stable after specification freeze.

Any incompatible encoding change requires
a new architecture revision.

Compatible implementations must interpret
the same binary program identically.

The physical memory profile must not alter
the meaning or length of an instruction.

## 15. Canonical Instruction Encoding

The following encoding is normative for
the RB4 base instruction set.

All base instructions use one, two or three
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

All other Control encodings are invalid
and cause FAULT.

### 15.2 LDI

First unit:

0001 R000

Second unit:

0000 IIII

R selects the destination register.

IIII represents the unsigned 4-bit immediate.

The valid immediate range is 0 through 15.

The second unit must have its upper four bits
set to zero.

### 15.3 MOV and Register Operations

MOV, ADD, SUB, AND and OR use the format:

OOOO D S 00

D selects the destination register.

S selects the source register.

The main opcode determines the operation.

### 15.4 INC and DEC

Format:

0110 O R 00

O = 0: INC

O = 1: DEC

R selects the register.

### 15.5 XOR and NOT

XOR:

1001 0 D S 0

D selects the destination register.

S selects the source register.

NOT:

1001 1 R 00

R selects the register.

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

A8 through A0 form the 9-bit logical data address.

The third unit must have bits 7 through 1
set to zero.

### 15.7 Absolute JMP

First unit:

1010 0000

Second unit:

AAAAAAAA

The second unit contains the absolute
8-bit logical instruction target.

### 15.8 Conditional Absolute Jumps

First unit:

1011 C000

Second unit:

AAAAAAAA

C = 0: JZ

C = 1: JNZ

The second unit contains the absolute
8-bit logical instruction target.

### 15.9 Relative Jumps

First unit:

1100 CC00

Second unit:

SSSSSSSS

CC = 00: JR

CC = 01: JRZ

CC = 10: JRNZ

CC = 11: invalid

The second unit contains a signed 8-bit
two's complement offset.

The offset is added to the PC value
immediately following the complete instruction.

The target is calculated modulo 256.

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

The allocation above fixes the main opcode groups.

The bit-level operand layouts and valid encodings
are defined in Section 15.

All defined base instruction encodings are normative.

Unassigned subopcodes and reserved encodings
cause FAULT in the base architecture.

## 17. LDI Format Validation

LDI always occupies exactly two logical
instruction units.

The first unit identifies the destination register.

The second unit must use the format:

0000 IIII

The upper four bits of the second unit
must be zero.

The lower four bits represent the immediate
value from 0 through 15.

Any other second-unit format causes FAULT.

Implementations must not truncate, wrap
or reinterpret an invalid LDI encoding.

## 18. Relative PC Boundary Rule

PC arithmetic is modular over 8 bits.

A complete instruction whose final logical
unit is at address 0xFF may advance the PC
to 0x00.

An instruction beginning at 0xFF that requires
one or more additional logical instruction units
is incomplete and causes FAULT.

Instruction fetch for an incomplete instruction
does not wrap around to address 0x00 to complete
that instruction.

## 19. Logical Data Address Width

Logical data addresses are 9 bits wide.

They identify one of 512 logical 4-bit positions.

The valid logical data address range is:

0x000 through 0x1FF.

The physical memory profile must not change
this logical address width or its meaning.

LOAD and STORE use the complete 9-bit
logical data address.

## 20. MMIO Data-Address Window

The logical data-address range 0x1F0 through
0x1FF is reserved for memory-mapped input/output.

LOAD and STORE accesses to this range are routed
to the MMIO interface rather than ordinary RAM.

Instruction fetch continues to use unified
program memory and is not redirected by
this data-access MMIO window.

The device-register assignment within the MMIO
window is defined by the selected platform profile.

An access to an unimplemented or forbidden
MMIO register causes FAULT.

A common peripheral register map remains
a future standardization task.
