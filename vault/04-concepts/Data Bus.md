---
tags:
  - story
source: "[[crash-course-cs-ep07]]"
---
## Definition
The physical "highway" (a collection of parallel wires) on the motherboard used to transfer the actual data payload bits back and forth between the CPU, RAM, and other hardware peripherals.

## Details
- **The Analogy:** If the CPU is a factory and RAM is the warehouse, the Data Bus is the multi-lane shipping highway connecting them.
- - **Bus Width (The Lanes):** The number of physical wires dictates the "width" of the bus. A 32-bit data bus has 32 wires and can move 32 bits (4 bytes) of data simultaneously in a single clock cycle. A 64-bit data bus has 64 wires and moves 64 bits (8 bytes) at once.
    
- **The Trio:** The data bus never works alone. It is part of the System Bus, which includes:
    
    - **Address Bus:** Specifies _where_ in RAM the data should be read from or written to.
        
    - **Control Bus:** Transmits the command (e.g., "Read" or "Write").
        
    - **Data Bus:** Carries the _actual content_ once the address and command are set.

## Why it matters to me as a dev
- **The 4GB RAM Limit:** This hardware architecture is the exact reason 32-bit operating systems and applications cannot utilize more than 4GB of RAM. A 32-bit bus architecture physically cannot address or move data beyond $2^{32}$ bytes of memory. Upgrading to 64-bit architectures blew past this bottleneck, allowing modern software to leverage massive datasets in memory.
    
- **Native Data Type Sizes:** The width of the data bus is the direct reason why standard integer types (`int` or `long`) and memory pointers in languages like C, Go, or Rust are typically 4 bytes on 32-bit systems and 8 bytes on 64-bit systems. Programming languages design their primitive types to match the data bus width so the CPU can fetch a variable in a single, efficient hardware cycle.

## In my words
Data bus is the physical wires that connect other components of a computer. There can be 32 or 64 parallel wires connecting from one component to another working as a unit, which represents the width of a bus.