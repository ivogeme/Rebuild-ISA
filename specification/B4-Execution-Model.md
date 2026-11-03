# RB4 Execution Model

**Version:** 0.1.0

## 1. Processor States

RB4 defines three architectural execution states:

- RUNNING
- HALTED
- FAULT

## 2. Initialization

After reset, the architectural registers are initialized
to zero:

- R0 = 0
- R1 = 0
- PC = 0
- FLAGS = 0

## 3. Reset

Reset terminates the current execution state,
clears the architectural registers and restarts
execution from logical address 0x00.

## 4. Instruction Execution

The processor fetches, decodes and executes
instructions according to the RB4 instruction set.

The logical instruction unit is 8 bits.

## 5. HALT

HALT places the processor into the HALTED state.

The PC points to the logical position following
the HALT instruction.

Execution resumes only after reset.

## 6. FAULT

FAULT represents an architectural execution error.

Invalid instructions and invalid instruction formats
may cause this state.

FAULT is cleared by reset.

## 7. Program Counter Overflow

The PC uses modular 8-bit arithmetic.

An increment beyond 0xFF wraps to 0x00.
