# RB4 Execution Model

**Version:** 0.3.0
**Status:** Draft

## 1. Overview

This document defines the architectural execution
behavior of RB4 processors.

It describes processor states, initialization,
instruction execution, reset and fault handling.

The execution model is independent of the
physical implementation.

## 2. Processor States

RB4 defines three architectural execution states:

- RUNNING
- HALTED
- FAULT

### RUNNING

The processor is actively fetching, decoding
and executing instructions.

### HALTED

The processor has stopped normal instruction execution.

The processor remains in this state until reset.

### FAULT

The processor has encountered an invalid
architectural instruction or execution condition.

The processor remains in this state until reset.

## 3. Initialization

After reset, the architectural registers
are initialized to zero:

- R0 = 0
- R1 = 0
- PC = 0
- FLAGS = 0

The processor enters the RUNNING state.

Execution begins at logical instruction address 0x00.

## 4. Reset

Reset terminates the current execution state.

It clears the architectural registers and
restarts execution.

The resulting architectural state is:

- R0 = 0
- R1 = 0
- PC = 0
- FLAGS = 0
- State = RUNNING

The architecture does not require a specific
electrical reset implementation.

Hardware implementations may use synchronous
or asynchronous reset circuitry.

The resulting architectural state must be identical.

## 5. Instruction Execution Cycle

A normal instruction execution follows
these logical stages:

1. Fetch.
2. Decode.
3. Fetch additional instruction units if required.
4. Execute.
5. Update architectural state.
6. Continue execution.

The implementation may combine or divide
these stages internally.

The architecture does not require a specific
number of clock cycles per instruction.

## 6. Instruction Fetch

The processor fetches the logical 8-bit
instruction unit identified by PC.

The physical memory profile determines
how the logical unit is retrieved.

In RB4-N, one logical instruction unit
occupies two physical 4-bit positions.

In RB4-B, one logical instruction unit
occupies one physical 8-bit position.

## 7. Instruction Decoding

The processor decodes the fetched instruction
according to the binary encoding specification.

If the instruction requires additional
logical instruction units, those units
must be fetched before execution.

The complete instruction length must be
determined from the instruction format.

## 8. Program Counter Updates

The PC identifies the next logical instruction.

For normal sequential execution, the PC
advances by the complete instruction length.

For branching instructions, the PC is updated
according to the specified branch behavior.

PC arithmetic uses modular 8-bit behavior.

An address beyond 0xFF wraps to 0x00.

## 9. Instruction Execution

Instructions modify architectural state
according to their defined semantics.

Arithmetic and logical operations use
4-bit register values.

Memory operations transfer logical 4-bit values.

Branch instructions update PC according
to their absolute or relative addressing mode.

Instructions that do not modify FLAGS
must preserve its previous value.

## 10. HALT

HALT places the processor into the HALTED state.

The PC points to the logical position
immediately following the HALT instruction.

No subsequent instruction is executed
while the processor remains HALTED.

Execution resumes only after reset.

## 11. FAULT

FAULT represents an architectural execution error.

The processor enters FAULT when it encounters:

- Undefined opcode.
- Undefined subopcode.
- Invalid instruction format.
- Invalid register selector.
- Invalid operand combination.
- Incomplete instruction.
- Invalid immediate value.
- Access to an unimplemented or forbidden MMIO register.

When FAULT occurs:

1. PC remains at the starting address
   of the faulting instruction.
2. R0 and R1 are not partially modified.
3. FLAGS is not partially modified.
4. No subsequent instruction is executed.
5. The processor remains in FAULT until RESET.

External MMIO effects that have already occurred
are not rolled back.

The processor must not continue normal execution
while in FAULT.


## 12. Incomplete Instructions

An extended instruction may require
additional logical instruction units.

If the complete instruction cannot be fetched
before the end of the logical instruction
address space, the processor enters FAULT.

The processor must not execute only part
of an extended instruction.

## 13. Program Counter Overflow

The PC uses modular 8-bit arithmetic.

An increment beyond 0xFF wraps to 0x00.

This behavior applies to sequential instruction
execution and relative branch calculations.

## 14. Memory Behavior

Instructions and data share the same physical memory.

Self-modifying code is permitted.

A memory write that modifies a future instruction
must be visible when that instruction is fetched.

The architecture does not require instruction
or data caches.

Implementations must preserve the specified
observable behavior.

## 15. Reset from HALTED or FAULT

Reset clears both HALTED and FAULT states.

After reset, the processor enters RUNNING
and begins execution from logical address 0x00.

## 16. Implementation Independence

The execution model does not prescribe:

- Clock frequency.
- Number of clock cycles per instruction.
- Internal pipeline structure.
- Internal control unit organization.
- Electrical reset circuitry.
- Internal register arrangement.

Different implementations are compatible
when they preserve the same architectural behavior.

## 17. Instruction Boundary at 0xFF

PC arithmetic remains modular over 8 bits.

A complete instruction ending at logical address 0xFF
advances the PC to 0x00.

However, an instruction that begins at 0xFF and requires
additional logical instruction units is incomplete and
causes FAULT.

The processor must not fetch the missing part from
address 0x00.

This rule distinguishes normal PC wrap after a complete
instruction from an instruction that crosses the end
of the logical instruction address space.

## 18. Extended LDI Fault Condition

An extended LDI immediate greater than 15 causes FAULT,
because the destination general-purpose register is only
4 bits wide.

No truncation or modulo conversion is permitted.

## 19. MMIO Fault Condition

LOAD and STORE accesses to logical data addresses
0x1F0 through 0x1FF are routed to MMIO.

If the selected platform profile does not implement
the addressed MMIO register, the access causes FAULT.

The MMIO register assignment is defined by the selected
platform profile.

## 20. Fault Atomicity

An instruction that causes FAULT must not leave
partially updated architectural register state.

The architectural state of R0, R1 and FLAGS
must remain unchanged by the failed instruction.

This requirement does not imply rollback of
external device effects that have already occurred.

The PC identifies the starting address of
the instruction that caused FAULT.


