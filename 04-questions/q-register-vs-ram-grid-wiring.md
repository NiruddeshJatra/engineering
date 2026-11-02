---
type: question
module: 1
status: answered
asked: 2026-04-24
ai: gemini
source:
concepts: []
follows: "[[q-256-bit-memory-vs-64-bit-word]]"
---
## My question
Still not clear. What I understood is: A latch can store/read/write one bit of information. We can organize 64 latches side by side to make register, that would need 1 write enable wire, 64 input wire, 64 output wire. This is lightening fast as we can get input and output from all latches at once. But it is costly because we need more wires. In another design, we can keep the 64 latches in a 8 * 8 grid with two multiplexer. That needs one input wire, one write enable wire, one read enable wire, 64 output wire, cheap, but time consuming. So, we can store any 64 bit of information using any of these formation - am I right?

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: You have a very solid grasp of the logic, but I need to point out one critical *error* in your understanding of the "Cheap/Grid" design.*
>
> ### *The Bitter Truth: The "One-Wire" Bottleneck*
>
> *You said: "In another design, we can keep the 64 latches in a 8 * 8 grid... cheap, but time consuming. So, we can store any 64 bit of information using any of these formation."*
>
> ***Correction:***
>
> *In the 8x8 grid (the matrix) described in the video, you **cannot** store or read a 64-bit number at once.*
>
> - *Because there is only **one** data input wire and **one** data output wire, you can only talk to **one bit at a time**.*
>
> - *If you wanted to store the 64-bit number `1011...`, you would have to:*
>
>     1. *Send bit 1 to address (0,0).*
>
>     2. *Send bit 2 to address (0,1).*
>
>     3. *...repeat this 64 times.*
>
> - *This is why it is "time-consuming." It’s not just slower; it fundamentally changes how the CPU has to interact with it.*
>
>
> *---*
>
> ### *Clarifying the Hardware Locations*
>
> *To understand why we use both, you have to look at where they live in the computer:*
>
> #### *1. The Register (Side-by-Side)*
>
> - ***What it stores:** A single 64-bit "word" (like the number `500` or a memory address).*
>
> - ***Speed:** Instant.*
>
> - ***Cost:** Very High.*
>
> - ***Where:** Inside the CPU's execution core.*
>
> - ***Analogy:** Your **hands**. You can hold a tool and use it immediately. You only have two hands because "wiring" more hands to your brain would be physically impossible.*
>
>
> #### *2. The 256-bit (or Gigabyte) RAM (The Grid)*
>
> - ***What it stores:** Millions of 64-bit words.*
>
> - ***Speed:** Slow (relatively).*
>
> - ***Cost:** Low.*
>
> - ***Where:** Outside the CPU on those green sticks of RAM.*
>
> - ***Analogy:** A **warehouse**. It stores millions of tools. You can't use them while they are in the warehouse. You have to go find the right "address," pick up one tool, and bring it to your "hands" (the register).*
>
>
> *---*
>
> ### *Addressing the "Output Wire" Error*
>
> *You mentioned the 8x8 grid needs "64 output wires." **That is incorrect.***
>
> *In a true matrix/grid memory:*
>
> - *You only have **one** output wire.*
>
> - *All 64 latches are connected to that same single output wire.*
>
> - *Because the "Read Enable" is only turned on for **one specific latch** (using the multiplexers), only that one latch is allowed to "shout" its value onto the wire. The others stay silent.*
>
> - *This is why we only need **1 wire** instead of 64.*
>
>
> ### *Summary of the "Trade-off"*
>
> - ***Register:** 64 bits of storage = 64 Data wires. (Fast, Huge, Expensive).*
>
> - ***Grid Memory:** 64 bits of storage = 1 Data wire. (Slow, Tiny, Cheap).*
>
>
> *In a modern 64-bit computer, we actually use a **hybrid**. We put 64 grids side-by-side. So when the CPU gives one address, all 64 grids give up 1 bit at the same time, resulting in a 64-bit "word." This is how we get the efficiency of the grid but the "width" the CPU needs.*
>
> *Does this distinction between "Parallel" (Registers) and "Serial/Addressed" (Grid) storage help clear the fog?
