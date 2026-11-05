# RB4 Register Specification

**Version:** 0.2.0
**Status:** Draft

## 1. Overview

RB4 defines four architectural registers:

- R0
- R1
- PC
- FLAGS

R0 and R1 are general-purpose registers.

PC controls instruction execution.

FLAGS stores the status of applicable operations.

Implementations may use additional internal registers,
but these must not be architecturally visible.

## 2. General-Purpose Registers

RB4 provides two general-purpose registers.

| Register | Width | Description |
|---|---|---|
| R0 | 4 bits | General-purpose register |
| R1 | 4 bits | General-purpose register |

Both registers store unsigned 4-bit values.

The unsigned representable range is:

0 to 15.

Signed values may be represented using 4-bit
two's complement.

The signed representable range is:

-8 to +7.

Both registers support arithmetic, logical and
data-transfer operations.

There is no mandatory zero register.

There is no mandatory architectural stack pointer.

## 3. Program Counter

| Register | Width | Description |
|---|---|---|
| PC | 8 bits | Program Counter |

The PC identifies the logical address of the
next instruction.

The logical instruction address range is:

0x00 to 0xFF.

The PC uses modular 8-bit arithmetic.

An increment beyond 0xFF wraps to 0x00.

The PC always identifies logical instruction units,
independently of the physical memory profile.

RB4-N and RB4-B must preserve identical PC behavior.

The physical memory mapping is defined separately.

## 4. Status Register

| Register | Width | Description |
|---|---|---|
| FLAGS | 2 bits | Operation status flags |

FLAGS contains two architectural bits.

| Bit | Name | Description |
|---|---|---|
| 0 | Z | Zero flag |
| 1 | C | Carry/Borrow flag |

All other bit positions are nonexistent.

## 5. Zero Flag

The Zero flag is named Z.

Z is set when the result of an applicable operation
is equal to zero.

Z is cleared when the result is nonzero.

The exact operations that modify Z are defined
in the instruction set specification.

## 6. Carry/Borrow Flag

The Carry/Borrow flag is named C.

For addition:

C indicates that the arithmetic operation
produced a carry beyond the 4-bit result.

For subtraction:

C indicates that the operation required
a borrow.

The flag is cleared when no carry or borrow occurs.

For logical operations, C is cleared.

## 7. Flag Update Rules

The following rules apply to the base instruction set.

| Instruction | Z | C |
|---|---|---|
| ADD | Updated | Updated with carry |
| SUB | Updated | Updated with borrow |
| INC | Updated | Updated with carry |
| DEC | Updated | Updated with borrow |
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

## 8. Internal Implementation Registers

Implementations may use additional internal registers
for instruction fetching, decoding and execution.

Examples include:

- Instruction register.
- Memory address register.
- Memory data register.
- Temporary arithmetic registers.
- Internal control registers.

These registers are not architecturally visible.

They are not required to have identical implementations.

## 9. Reset Values

After architectural reset:

- R0 = 0
- R1 = 0
- PC = 0
- FLAGS = 0

The processor enters the RUNNING state.

The detailed reset behavior is defined in
the execution model specification.

## 10. Compatibility

Compatible RB4 implementations must preserve
the register widths and behaviors defined here.

Internal implementation differences must not
change the architectural state visible to software.
