# RB4 Conformance Tests

**Version:** 0.2.0
**Status:** Draft

## 1. Overview

This document defines the mandatory conformance
tests for RB4 implementations.

Its purpose is to ensure that independent
hardware and software implementations preserve
the same architectural behavior.

Conformance tests apply to:

- Hardware processors.
- Software emulators.
- FPGA implementations.
- Experimental processor designs.

## 2. General Requirements

A compatible implementation must pass
all mandatory tests applicable to its
supported memory profile.

Tests must verify architectural results,
not internal implementation details.

Different implementations may use different
internal hardware and execution methods.

## 3. Register Tests

The implementation must correctly support:

- R0 as a 4-bit register.
- R1 as a 4-bit register.
- PC as an 8-bit register.
- FLAGS as a 2-bit register.

Tests must verify that general-purpose
register values remain within the range
0 to 15.

Tests must verify that PC wraps modulo 256.

## 4. Reset Tests

After reset, the following state is required:

- R0 = 0
- R1 = 0
- PC = 0
- FLAGS = 0
- State = RUNNING

Reset must clear HALTED and FAULT states.

## 5. Arithmetic Tests

The following operations must be tested:

- ADD
- SUB
- INC
- DEC

Tests must include:

- Zero results.
- Maximum unsigned values.
- Overflow.
- Underflow.
- Carry generation.
- Borrow generation.

Example ADD test:

R0 = 15
R1 = 1

ADD R0, R1

Expected result:

R0 = 0
C = 1
Z = 1

Example SUB test:

R0 = 0
R1 = 1

SUB R0, R1

Expected result:

R0 = 15
C = 1
Z = 0

## 6. Logical Tests

The following operations must be tested:

- AND
- OR
- XOR
- NOT

Tests must verify all four bits
of the resulting register value.

Tests must verify that Z is updated correctly.

Tests must verify that C is cleared.

## 7. Data Transfer Tests

The following operations must be tested:

- LDI
- MOV
- LOAD
- STORE

Tests must verify that:

- Immediate values are loaded correctly.
- Register values are copied correctly.
- LOAD retrieves the correct 4-bit value.
- STORE writes the correct 4-bit value.
- Unmodified memory values remain unchanged.
- MOV and LDI preserve FLAGS.
- LOAD and STORE preserve FLAGS.

## 8. Branching Tests

The following operations must be tested:

- JMP
- JZ
- JNZ

Tests must verify:

- Absolute branching.
- Relative branching.
- Positive relative offsets.
- Negative relative offsets.
- Branches based on Z = 1.
- Branches based on Z = 0.
- PC wrapping behavior.

Relative offsets must be calculated
from the address immediately following
the complete branch instruction.

## 9. Control Tests

The following instructions must be tested:

- NOP
- HALT

NOP must not modify architectural
registers or FLAGS.

HALT must enter the HALTED state.

The PC must identify the logical position
immediately following HALT.

Execution must remain stopped until reset.

## 10. Memory Profile Tests

RB4-N and RB4-B implementations must
pass equivalent logical memory tests.

Tests must verify:

- Correct memory capacity.
- Correct instruction fetching.
- Correct logical data access.
- Correct LOAD behavior.
- Correct STORE behavior.
- Correct unified memory behavior.
- Correct self-modifying code behavior.

## 11. Unified Memory Tests

Tests must verify that instruction
and data accesses refer to the same
underlying memory.

A program must be able to modify
a future instruction through STORE.

The subsequent instruction fetch must
observe the modified contents.

## 12. Encoding Tests

Tests must verify:

- Valid opcode decoding.
- Valid subopcode decoding.
- Valid register selectors.
- Valid immediate formats.
- Valid extended instruction formats.
- Correct instruction lengths.

Reserved and invalid encodings must
not execute as valid instructions.

## 13. Fault Tests

The implementation must enter FAULT
when encountering:

- Undefined opcode.
- Undefined subopcode.
- Invalid register selector.
- Invalid operand combination.
- Invalid instruction format.
- Incomplete extended instruction.

The processor must remain in FAULT
until reset.

## 14. Cross-Implementation Tests

Equivalent programs must produce
identical architectural results across
compatible implementations.

Where applicable, tests must compare:

- RB4-N hardware.
- RB4-B hardware.
- Software emulators.
- FPGA implementations.

The internal implementation may differ.

The architectural result must remain identical.

## 15. Test Suite Requirements

The official RB4 test suite should provide:

- Binary test programs.
- Expected register states.
- Expected memory states.
- Expected FLAGS values.
- Expected PC values.
- Expected execution states.

Tests must be versioned alongside
the architectural specification.

## 16. Conformance Status

An implementation may claim RB4 compatibility
only when it passes all mandatory tests
for its declared memory profile.

Partial implementations must clearly
identify unsupported architectural features.


## 17. Ratified Decision Tests

The conformance suite must additionally verify:

- Logical data addresses are 9 bits and cover 0x000–0x1FF.
- RB4-B selector 0 reads/writes bits 3:0 and selector 1
  reads/writes bits 7:4.
- A STORE to one RB4-B nibble preserves the other nibble.
- RB4-N instruction assembly uses the first physical nibble
  as bits 3:0 and the second as bits 7:4.
- Extended LDI values greater than 15 cause FAULT.
- A complete instruction ending at 0xFF advances PC to 0x00.
- An instruction beginning at 0xFF that requires another
  logical unit causes FAULT and does not wrap to fetch it.
- LOAD and STORE accesses to 0x1F0–0x1FF are routed to MMIO.
- Access to an unimplemented MMIO register causes FAULT.
- The main opcode group values match the approved table
  in RB4-Binary-Encoding.md.

The final binary test programs and expected states must be
added when the complete subopcode and operand encoding is
published.

## 18. Fault Atomicity Tests

The conformance suite must verify that
a faulting instruction:

- Leaves PC at the starting instruction address.
- Does not partially modify R0.
- Does not partially modify R1.
- Does not partially modify FLAGS.
- Prevents subsequent instructions from executing.
- Leaves the processor in FAULT until RESET.

External MMIO effects already performed
are not required to be rolled back.

Tests must also verify that RESET clears
the FAULT state and restores the initial
architectural register values.

