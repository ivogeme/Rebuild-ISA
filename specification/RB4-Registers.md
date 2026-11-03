# RB4 Register Specification

**Version:** 0.1.0

## 1. General Registers

RB4 provides two general-purpose registers.

| Register | Width | Description |
|---|---|---|
| R0 | 4 bits | General-purpose register |
| R1 | 4 bits | General-purpose register |

Both registers support arithmetic, logical and
data-transfer operations as defined by the ISA.

## 2. Program Counter

| Register | Width | Description |
|---|---|---|
| PC | 8 bits | Program Counter |

The PC identifies the next logical instruction address.

It wraps around modulo 256.

## 3. Status Register

| Register | Width | Description |
|---|---|---|
| FLAGS | 2 bits | Operation status flags |

### Zero Flag (Z)

Bit 0.

Set when the result of an applicable operation is zero.

### Carry/Borrow Flag (C)

Bit 1.

For addition, C indicates an arithmetic carry.

For subtraction, C indicates a borrow.

## 4. Internal Implementation Registers

Implementations may use additional internal registers
for instruction fetching, decoding and execution.

These registers are not architecturally visible and
are not required to have identical implementations.
