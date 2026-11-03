# RB4 Memory Model

**Version:** 0.1.0

## 1. Overview

RB4 uses a unified memory model.

Instructions and data share the same address space.

The architecture defines two memory profiles.

## 2. RB4-N: Nibble Memory

RB4-N uses 4-bit addressable memory units.

Characteristics:

- 256 addressable positions.
- 4 bits per position.
- 1024 bits of total storage.
- 128 bytes of equivalent capacity.

Each logical 8-bit instruction occupies two
consecutive physical memory positions.

## 3. RB4-B: Byte Memory

RB4-B uses 8-bit addressable memory units.

Characteristics:

- 256 addressable positions.
- 8 bits per position.
- 2048 bits of total storage.
- 256 bytes of capacity.

Each logical 8-bit instruction occupies one
physical memory position.

## 4. Memory Operations

The architecture provides:

- LOAD: Read data from memory.
- STORE: Write data to memory.

The initial addressing model uses absolute addresses.

## 5. Unified Memory

Program instructions and data share the same memory.

Self-modifying code is permitted.

No memory protection mechanism is mandatory.

## 6. Memory-Mapped Input/Output

RB4 uses memory-mapped input/output.

External devices may be accessed through reserved
memory addresses.

Possible devices include:

- LEDs.
- Buttons.
- Displays.
- Keyboards.
- Serial interfaces.
- Timers.

The exact device address map is defined by the
implementation or a future standard extension.
