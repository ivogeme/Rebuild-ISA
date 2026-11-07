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
units when extended operands are required.

## 2. Instruction Categories

The proposed base instruction set contains:

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
| JMP | Unconditional jump |
| JZ | Jump if zero |
| JNZ | Jump if not zero |

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

The exact address representation is defined
in the addressing specification.

LOAD does not modify FLAGS.

### 4.4 STORE

STORE writes a 4-bit register value to memory.

Example:

STORE R0, address

Result:

memory[address] = R0

The exact address representation is defined
in the addressing specification.

STORE does not modify FLAGS.



## 5. Arithmetic Instructions

Arithmetic operations produce 4-bit results.

The stored result is calculated modulo 16.

The Carry/Borrow flag records the condition
that occurred beyond the 4-bit result.

Invalid instruction operands and invalid
immediate values must cause FAULT..

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

JMP performs an unconditional jump.

The PC is changed to the specified target address.

### 7.2 JZ

JZ performs a conditional jump.

The jump occurs when Z = 1.

If Z = 0, execution continues with the next instruction.

### 7.3 JNZ

JNZ performs a conditional jump.

The jump occurs when Z = 0.

If Z = 1, execution continues with the next instruction.

### 7.4 Relative Branches

RB4 provides three relative branch operations:

JR
JRZ
JRNZ

JR always branches.

JRZ branches when Z = 1.

JRNZ branches when Z = 0.

The signed 8-bit offset is added to the
logical address immediately following
the complete branch instruction.

The calculation uses modular 8-bit PC arithmetic.

Relative branches do not modify FLAGS.


## 8. Absolute Branching

Absolute jumps use an explicit logical
instruction address.

The target address is interpreted as an
8-bit logical instruction address.

The PC is updated to the specified target.

## 9. Relative Branching

Relative jumps use signed offsets represented
in two's complement.

The offset is added to the logical address
of the instruction immediately following
the branch instruction.

The result uses modular 8-bit PC arithmetic.

The exact offset encoding is defined in
the binary encoding specification.

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
| NOP | Unchanged | Unchanged |
| HALT | Unchanged | Unchanged |

## 11. Control Instructions

### 11.1 NOP

NOP performs no architectural operation.

It does not modify registers or FLAGS.

The PC advances according to the instruction length.

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

Additional logical instruction units may be used
for extended operands.

The final opcode and subopcode allocation
is defined in the binary encoding specification.

## 13. Invalid Instructions

Invalid opcodes, invalid operand combinations
and invalid instruction formats cause FAULT.

The processor must not silently execute
an undefined instruction as another operation.

## 14. Compatibility

The meaning of existing instructions must not
change after the specification is finalized.

New instructions must use reserved encodings
or be introduced through a documented
architectural extension.


## 15. Extended LDI Validation

LDI writes a 4-bit value to its destination register.

Therefore, the valid immediate range is 0 through 15,
regardless of whether the short or extended encoding is used.

An extended LDI immediate greater than 15 causes FAULT.

The implementation must not truncate the value, wrap it
modulo 16, or silently accept it.

## 16. Memory-Mapped I/O Accesses

LOAD and STORE use 9-bit logical data addresses.

Addresses 0x1F0 through 0x1FF are reserved for MMIO
and access the MMIO interface instead of ordinary RAM.

An access to an unimplemented MMIO register causes FAULT.

The individual register assignments are defined by the
selected platform profile.
