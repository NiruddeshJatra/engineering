---
type: question
module: 1
status: answered
asked: 2026-04-24
ai: gemini
source:
concepts: []
---
## My question
What does 256 bits of memory store? Just 1 bit or 256 bit? In a 64 bit computer, everything is represented by 64 sequence of 0s and 1s. In those computers, what type of register is used? If 64 bit (8 * 8) is used, why have we studied 256 bits of memory?! What's the difference between a Register and a 256-bit memory?

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> A: *Based on the transcript you provided, you are hitting a classic point of confusion in computer architecture: the difference between **capacity** (how much it holds) and **word size** (how much it handles at once).*
> *Here is the rigorous breakdown of your questions.*
> ## *1. What does 256 bits of memory store?*
>
> *It stores exactly **256 individual bits** of information.*
>
> - *It does **not** store just 1 bit.*
>
> - *However, in the specific matrix design mentioned in your tutorial, you can only **access (read or write)** 1 bit at a time because there is only one "Data" wire.*
>
> - *Think of it like a hotel with 256 rooms. The hotel "stores" 256 people, but the door is only wide enough to let 1 person in or out at a time.*
>
>
> *---*
>
> ## *2. Why study 256-bit memory if computers are 64-bit?*
>
> *You are comparing two different things: **Total Memory Capacity** vs. **CPU Word Size**.*
>
> - ***Total Capacity (The 256 bits):** This is just a small example of storage. A 64-bit computer actually has billions of bits of RAM (gigabytes). The "256 bits" in your tutorial is just a training-wheels version to show you how a matrix works.*
>
> - ***Word Size (The 64-bit part):** When we say a computer is "64-bit," it means the CPU's "hands" are big enough to grab 64 bits at once.*
>
> - ***The Conflict:** If you have 256 bits of memory but a 64-bit CPU, the CPU would finish reading the entire memory in just 4 "grabs" ($256 / 64 = 4$). We study the 256-bit model to understand the **logic of the grid**, not because it’s a standard size for a modern PC.*
>
>
> *---*
>
> ## *3. Register vs. 256-bit Memory*
>
> *The transcript explains this transition, but here is the "bitter truth" about the hardware difference:*
>
> |**Feature**|**Register**|**256-bit Memory (SRAM Matrix)**|
> |---|---|---|
> |**Structure**|Latches placed side-by-side in a row.|Latches arranged in a 2D grid (Matrix).|
> |**Wiring**|**Expensive.** Needs a wire for every single bit (e.g., 64 wires for 64 bits).|**Efficient.** Uses "Address" wires and a grid to share wires.|
> |**Access**|**Instant.** All bits are available simultaneously.|**Slower.** You have to "address" the row and column to find the bit.|
> |**Location**|Inside the CPU (The "Work Desk").|Outside the CPU (The "Warehouse").|
> |**Purpose**|To hold data the CPU is using _right now_.|To store data the CPU _might_ need soon.|
>
> *---*
>
> ## *4. What type of registers are used in 64-bit computers?*
>
> *In a 64-bit computer, the registers are **General Purpose Registers (GPRs)** that are **64 bits wide**.*
>
> - *They are made of 64 latches/flip-flops linked together.*
>
> - *When the tutorial mentioned "8 * 8," it was likely referring to the **grid layout** for a 64-bit memory module, not the register itself.*
>
> - *Registers don't usually use the "grid" (matrix) layout because they need to be lightning-fast. They use the "side-by-side" layout, which uses more wires but allows the CPU to see all 64 bits at the exact same time.*
>
>
> ### *The Error in Your Thinking*
>
> *You asked: "If 64 bit (8 * 8) is used, why have we studied 256 bits of memory?"*
>
> *The "8 * 8" grid and the "256-bit" grid are just different sizes of the same technology (RAM). You study the 256-bit version because it uses an **8-bit address** ($2^8 = 256$), which is a perfect mathematical example for students to learn how binary addresses map to a physical location.*
>
> *Does the distinction between "how much we can store" and "how much we can grab at once" make sense now?*
