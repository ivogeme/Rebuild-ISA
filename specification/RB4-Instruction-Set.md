# RB4 Instruction Set

**Version:** 0.1.0  
**Status:** Draft

## 1. Overview

The RB4 instruction set provides basic arithmetic,
logical, memory and control operations.

The base instruction set is designed to minimize
implementation complexity.

## 2. Proposed Instructions

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
| XOR | Bitwise XOR |
| NOT | Bitwise NOT |

### Branching

| Instruction | Description |
|---|---|
| JMP | Unconditional jump |
| JZ | Jump if zero |
| JNZ | Jump if not zero |

Both absolute and relative jump addressing
are intended to be supported.

## 3. Arithmetic Behavior

Arithmetic results are stored using 4-bit values.

Addition updates the Carry flag.

Subtraction updates the Borrow flag.

The Zero flag indicates whether the resulting
4-bit value is zero.

## 4. Logical Operations

Logical operations update the Zero flag
and clear the Carry flag.

MOV and LDI do not modify FLAGS.

## 5. Instruction Encoding

The final opcode allocation and binary encoding
are defined in a separate specification document.
