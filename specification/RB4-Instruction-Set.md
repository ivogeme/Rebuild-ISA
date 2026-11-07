# RB4 Instruction Set

**Version:** 0.3.0
**Status:** Draft

## 1. Overview

The RB4 instruction set provides basic arithmetic,
logical, memory and control operations.

The base instruction set is designed to minimize
implementation complexity.

All general-purpose arithmetic and logical operations
operate on 4-bit values.

The architecture provides two general-purpose registers:

- R0
- R1

Instructions may use additional logical instruction
units when operands or addresses require them.

## 2. Instruction Categories

The RB4 base instruction set contains:

### Control

| Instruction | Description |
|---|---|
| NOP | No operation |
| HALT | Stop execution |

### Data Transfer

| Instruction | Description |
|---|---|
| LDI | Load immediate value |
| MOV | Copy register value |
| LOAD | Read memory |
| STORE | Write memory |

### Arithmetic

| Instruction | Description |
|---|---|
| ADD | Addition |
| SUB | Subtraction |
| INC | Increment |
| DEC | Decrement |

### Logical

| Instruction | Description |
|---|---|
| AND | Bitwise AND |
| OR | Bitwise OR |
| XOR | Bitwise exclusive OR |
| NOT | Bitwise inversion |

### Branching

| Instruction | Description |
|---|---|
| JMP | Absolute unconditional jump |
| JZ | Absolute jump if zero |
| JNZ | Absolute jump if not zero |
| JR | Relative unconditional jump |
| JRZ | Relative jump if zero |
| JRNZ | Relative jump if not zero |

Both absolute and relative jump addressing
are supported by the architecture.

## 3. Register Operand Convention

Instructions using two general-purpose registers
follow a destination-first convention.

The first register is the destination.

The second register is the source.

For example:

ADD R0, R1

means:

R0 = R0 + R1

The result is stored in R0.

The original value of R1 remains unchanged.

The same convention applies to SUB and
the two-register logical operations.

## 4. Data Transfer Instructions

### 4.1 LDI

LDI loads a 4-bit immediate value into
a selected general-purpose register.

Syntax:

LDI R0, immediate
LDI R1, immediate

The valid immediate range is 0 through 15.

LDI always occupies two logical instruction units.

LDI does not modify FLAGS.

Examples:

LDI R0, 5
LDI R1, 15

### 4.2 MOV

MOV copies the contents of one register
into another register.

Example:

MOV R0, R1

Result:

R0 = R1

The source register remains unchanged.

MOV does not modify FLAGS.

### 4.3 LOAD

LOAD reads a 4-bit logical value from memory
and stores it in a general-purpose register.

Example:

LOAD R0, address

Result:

R0 = memory[address]

The address is a 9-bit logical data address.

LOAD does not modify FLAGS.

### 4.4 STORE

STORE writes a 4-bit register value to memory.

Example:

STORE R0, address

Result:

memory[address] = R0

The address is a 9-bit logical data address.

STORE does not modify FLAGS.

Addresses 0x1F0 through 0x1FF are reserved
for MMIO data accesses.

## 5. Arithmetic Instructions

Arithmetic operations produce 4-bit results.

The stored result is calculated modulo 16.

The Carry/Borrow flag records the condition
that occurred beyond the 4-bit result.

Invalid instruction operands and invalid
instruction formats must cause FAULT.

### 5.1 ADD

ADD performs 4-bit addition.

Example:

ADD R0, R1

Result:

R0 = (R0 + R1) modulo 16

The Zero and Carry flags are updated.

C is set when the unsigned addition produces
a carry beyond bit 3.

### 5.2 SUB

SUB performs 4-bit subtraction.

Example:

SUB R0, R1

Result:

R0 = (R0 - R1) modulo 16

The Zero and Carry flags are updated.

C is set when the unsigned subtraction
requires a borrow.

### 5.3 INC

INC increments a selected register by one.

Example:

INC R0

Result:

R0 = (R0 + 1) modulo 16

The Zero and Carry flags are updated.

C is set when the original register value
was 15.

### 5.4 DEC

DEC decrements a selected register by one.

Example:

DEC R0

Result:

R0 = (R0 - 1) modulo 16

The Zero and Carry flags are updated.

C is set when the original register value
was 0.

## 6. Logical Instructions

All logical instructions operate on individual bits
of 4-bit register values.

### 6.1 AND

Performs bitwise AND.

Example:

AND R0, R1

Result:

R0 = R0 AND R1

### 6.2 OR

Performs bitwise OR.

Example:

OR R0, R1

Result:

R0 = R0 OR R1

### 6.3 XOR

Performs bitwise exclusive OR.

Example:

XOR R0, R1

Result:

R0 = R0 XOR R1

### 6.4 NOT

Inverts all four bits of a selected register.

Example:

NOT R0

Result:

R0 = NOT R0

Only the lowest four bits are retained.

### 6.5 Logical Flag Behavior

All logical instructions update Z.

Z is set when the resulting 4-bit value is zero.

C is cleared.

## 7. Branching Instructions

### 7.1 JMP

JMP performs an unconditional absolute jump.

The PC is changed to the specified
8-bit logical instruction target address.

### 7.2 JZ

JZ performs a conditional absolute jump.

The jump occurs when Z = 1.

If Z = 0, execution continues with the
logical instruction immediately following JZ.

### 7.3 JNZ

JNZ performs a conditional absolute jump.

The jump occurs when Z = 0.

If Z = 1, execution continues with the
logical instruction immediately following JNZ.

### 7.4 Relative Branches

RB4 provides three relative branch operations:

- JR
- JRZ
- JRNZ

JR always branches.

JRZ branches when Z = 1.

JRNZ branches when Z = 0.

The signed 8-bit offset is added to the
logical address immediately following
the complete branch instruction.

The target is calculated as:

Target = (PC_after_instruction + SignExtend(offset)) mod 256

Relative branches do not modify FLAGS.

## 8. Absolute Branching

Absolute jumps use an explicit logical
instruction address.

The target address is an 8-bit value
from 0x00 through 0xFF.

The PC is updated to the specified target.

## 9. Relative Branching

Relative jumps use signed 8-bit offsets
represented in two's complement.

The offset range is:

-128 to +127.

The offset is added to the logical address
immediately following the complete
branch instruction.

The result uses modular 8-bit PC arithmetic.

The binary encoding is defined in
`RB4-Binary-Encoding.md`.

## 10. Flag Update Summary

| Instruction | Z behavior | C behavior |
|---|---|---|
| ADD | Updated | Carry |
| SUB | Updated | Borrow |
| INC | Updated | Carry |
| DEC | Updated | Borrow |
| AND | Updated | Cleared |
| OR | Updated | Cleared |
| XOR | Updated | Cleared |
| NOT | Updated | Cleared |
| MOV | Unchanged | Unchanged |
| LDI | Unchanged | Unchanged |
| LOAD | Unchanged | Unchanged |
| STORE | Unchanged | Unchanged |
| JMP | Unchanged | Unchanged |
| JZ | Unchanged | Unchanged |
| JNZ | Unchanged | Unchanged |
| JR | Unchanged | Unchanged |
| JRZ | Unchanged | Unchanged |
| JRNZ | Unchanged | Unchanged |
| NOP | Unchanged | Unchanged |
| HALT | Unchanged | Unchanged |

## 11. Control Instructions

### 11.1 NOP

NOP performs no architectural operation.

It does not modify registers or FLAGS.

The PC advances by one logical instruction unit.

### 11.2 HALT

HALT places the processor in the HALTED state.

The PC points to the logical position immediately
following the HALT instruction.

Execution remains stopped until reset.

## 12. Instruction Encoding

The logical instruction unit is 8 bits.

The basic instruction format uses:

- 4-bit opcode field.
- 4-bit operand field.

Base instructions occupy one, two or three
logical instruction units.

The canonical opcode and operand allocation
is defined in `RB4-Binary-Encoding.md`.

## 13. Invalid Instructions

Invalid opcodes, invalid operand combinations,
invalid register selectors and invalid instruction
formats cause FAULT.

Reserved bits must have the values specified
by the canonical binary encoding.

The processor must not silently execute
an undefined instruction as another operation.

## 14. Compatibility

The meaning of existing instructions must not
change after the specification is finalized.

New instructions must use reserved encodings
or be introduced through a documented
architectural extension.

An incompatible change requires a new
architecture revision.

## 15. Canonical LDI Validation

LDI always occupies exactly two logical
instruction units.

The first unit identifies the destination register.

The second unit must use the format:

0000 IIII

The upper four bits of the second unit
must be zero.

The lower four bits represent the immediate value
from 0 through 15.

Any invalid LDI encoding causes FAULT.

No short or extended LDI alternative encoding exists.

Implementations must not truncate, wrap
or reinterpret an invalid immediate value.

## 16. Memory-Mapped I/O Accesses

LOAD and STORE use 9-bit logical data addresses.

Addresses 0x1F0 through 0x1FF are reserved
for MMIO and access the MMIO interface
instead of ordinary RAM.

An access to an unimplemented or forbidden
MMIO register causes FAULT.

The individual register assignments are defined
by the selected platform profile.

Instruction fetching is not redirected
by the MMIO data-address window.
