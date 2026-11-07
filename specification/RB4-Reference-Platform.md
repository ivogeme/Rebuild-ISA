# RB4 Reference Platform Profile

**Profile:** RB4-REF-REG-1
**Version:** 1.0
**Status:** Reference

## 1. Purpose

This document defines a deterministic reference
MMIO platform profile for RB4.

Its purpose is to provide a common register map
for hardware implementations, software emulators
and conformance testing.

This profile defines a simple memory-mapped
register bank.

It does not require physical LEDs, buttons,
displays, timers or serial interfaces.

Physical platforms may define additional
or different peripherals through their own
platform profiles.

## 2. Address Map

The reference profile implements all sixteen
logical data addresses reserved for MMIO.

Each register stores one 4-bit unsigned value.

| Logical Address | Register | Access | Reset Value |
|---|---|---|---|
| 0x1F0 | REG0 | Read/Write | 0x0 |
| 0x1F1 | REG1 | Read/Write | 0x0 |
| 0x1F2 | REG2 | Read/Write | 0x0 |
| 0x1F3 | REG3 | Read/Write | 0x0 |
| 0x1F4 | REG4 | Read/Write | 0x0 |
| 0x1F5 | REG5 | Read/Write | 0x0 |
| 0x1F6 | REG6 | Read/Write | 0x0 |
| 0x1F7 | REG7 | Read/Write | 0x0 |
| 0x1F8 | REG8 | Read/Write | 0x0 |
| 0x1F9 | REG9 | Read/Write | 0x0 |
| 0x1FA | REG10 | Read/Write | 0x0 |
| 0x1FB | REG11 | Read/Write | 0x0 |
| 0x1FC | REG12 | Read/Write | 0x0 |
| 0x1FD | REG13 | Read/Write | 0x0 |
| 0x1FE | REG14 | Read/Write | 0x0 |
| 0x1FF | REG15 | Read/Write | 0x0 |

## 3. Register Behavior

A LOAD operation targeting an implemented
register returns its current 4-bit value.

A STORE operation targeting an implemented
register replaces its current 4-bit value.

Reads and writes have no additional side effects.

There are no read-to-clear operations,
write-one-to-clear operations or external
handshake requirements.

All sixteen registers are implemented
and writable.

## 4. Reset Behavior

Platform reset initializes all sixteen
registers to 0x0.

An implementation claiming compatibility
with this profile must provide a way to
initialize the register bank to this state.

Processor reset behavior remains defined
by the RB4 Execution Model.

## 5. Memory Overlay

LOAD and STORE operations targeting
0x1F0–0x1FF access the reference register bank.

These operations do not access ordinary RAM.

Instruction fetching continues to read
the unified program memory normally.

The MMIO register bank does not redirect,
replace or modify instruction fetching.

## 6. Compatibility

An implementation claiming support for
RB4-REF-REG-1 must implement all sixteen
registers according to this document.

All registers must support the specified
read, write and reset behavior.

The physical implementation may use any
suitable hardware or software mechanism,
provided that the architectural behavior
remains identical.

## 7. Conformance

The reference profile is used by the
MMIO conformance vectors provided in:

`tests/rb4-reference-profile-v1.json`

The profile is optional for the base RB4 ISA.

The base architecture requires the MMIO
address window and its routing behavior.

The selected platform profile determines
the actual register assignments.
