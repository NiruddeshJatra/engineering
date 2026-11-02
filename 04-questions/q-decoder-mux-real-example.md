---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
follows: "[[q-multiplexer-vs-decoder]]"
---
## My question
Your example is confusing. In both cases, principal types an address and both device connects room 2. If this is not satisfactory, use an actual example that can be described as a process happening inside a computer.

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: Let's refine that analogy with a real-world process inside your computer's memory. This should make the distinction between the **Decoder** 🗝️ and the **Multiplexer** 🚦 much sharper.*
>
> *Imagine we want to **Read** a single bit from a 4-bit memory. The memory is arranged in a grid with 2 rows and 2 columns.*
>
> ### *1. The Decoder: Finding the "Row" 🗝️*
>
> *The CPU says, "I want to look at **Row 1**." It sends the binary address `1` to the **Decoder**.*
>
> - ***The Process:** The Decoder has two output wires (Row 0 and Row 1). Because the input is `1`, it sends electricity down the **Row 1 wire** ONLY.*
>
> - ***The Result:** All the latches in Row 1 are now "unlocked" and ready to talk. Row 0 remains completely "locked."*
>
> - ***Key takeaway:** The Decoder takes a number and turns on **one specific path**.*
>
>
> ### *2. The Multiplexer: Choosing the "Column" 🚦*
>
> *Now, both latches in Row 1 are shouting their data. But the CPU only has **one** ear (one Data Bus wire). It only wants the bit from **Column 0**.*
>
> - ***The Process:** The **Multiplexer** sits at the end of the columns. It has two data inputs (one from Column 0 and one from Column 1) and one output wire.*
>
> - ***The Selection:** The CPU sends the address `0` to the Multiplexer's "select" pin. The Multiplexer acts like a gatekeeper—it lets the data from **Column 0** pass through to the CPU and blocks Column 1.*
>
> - ***Key takeaway:** The Multiplexer takes many existing signals and **filters them down to one**.*
>
>
> *---*
>
> ### *In the $143 + 69$ Example:*
>
> 1. *The **Decoder** is the "Address Specialist": It uses the address `1005` to physically energize the wires for the correct Row and Column in the RAM grid.*
>
> 2. *The **Multiplexer** is the "Data Traffic Controller": Once those spots are found, the MUX ensures that the bit from that specific location—and only that location—gets put onto the Data Bus highway.*
>
>
> *Does it help to think of the **Decoder** as "Powering Up" a location and the **Multiplexer** as "Routing" the data out of a location? 🗺️*
