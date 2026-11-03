# RB4 Binary Instruction Encoding

**Version:** 0.1.0  
**Status:** Pending Definition

## 1. Instruction Unit

The logical instruction unit is 8 bits wide.

## 2. Basic Instruction Format

The proposed basic format is:

| Field | Width |
|---|---:|
| Opcode | 4 bits |
| Operand | 4 bits |

## 3. Extended Instructions

Instructions may contain additional 8-bit units
for immediate values, addresses or offsets.

## 4. Branch Encoding

Absolute jumps use explicit logical addresses.

Relative jumps use signed offsets represented
in two's complement.

## 5. Opcode Allocation

Opcode allocation is not finalized.

Unused opcode values are intended to be reserved
for future architectural extensions.

## 6. Invalid Instructions

Invalid opcodes and invalid instruction formats
enter the architectural FAULT state.

## 7. Compatibility

Final encoding rules must preserve the meaning
of instructions after specification freeze.
