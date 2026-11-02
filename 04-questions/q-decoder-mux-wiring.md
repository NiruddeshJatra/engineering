---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
follows: "[[q-address-row-column-bits]]"
---
## My question
So, it is a 3-way setup, right? What I mean by that is, from CPU, 4 wires get connected to decoder and 4 wires to the Multiplexer. When CPU sends an address signal, the first 4 bits goes to decoder and last 4 bits goes to multiplexer through the wires. Decoder activates the requested row wire and multiplexer activates the requested column wire, which ultimately activates the specific latch of the grid and makes it read/write enabled. If I am right, both decoder and multiplexer is connected to 4 input wires from the CPU and multiple output wires from row and column of the RAM respectively.

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> A: You've absolutely nailed the "horizontal" vs. "vertical" selection logic! 🎯 It is indeed a multi-way setup where the **Address Bus** (the set of wires carrying the address) is split to handle different dimensions of the memory grid. 🧭
>
> Let's refine those connections to see exactly how they "talk" to the RAM:
>
> ### 1. The Decoder (Row Selector) 🗝️
>
> - **Input:** It takes the **first 4 bits** from the CPU. 📥
>
> - **Output:** It has **16 output wires**, each connected to a different **Row** in the grid. 📤
>
> - **Action:** It "decodes" that 4-bit number and sends electricity down exactly **one** of those 16 wires. This "wakes up" every latch in that specific row.
>
>
> ### 2. The Multiplexer (Column Selector) 🚦
>
> - **Input:** It takes the **last 4 bits** from the CPU as its "selector" signal. It also has **16 data inputs** (one from every column). 📥
>
> - **Output:** It has **one single output wire** connected to the CPU's Data Bus. 📤
>
> - **Action:** While the decoder has woken up an entire row, the Multiplexer acts like a guard. It looks at its 4-bit address and says, "I'm only going to let the data from **Column X** pass through to the CPU."
>
>
> ### The Result 📍
>
> Because the **Decoder** only powers up one row, and the **Multiplexer** only listens to one column, only the **one specific latch** at that intersection successfully sends its data back to the CPU.
>
> ---
