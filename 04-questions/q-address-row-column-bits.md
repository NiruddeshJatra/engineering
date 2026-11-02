---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
follows: "[[q-decoder-mux-real-example]]"
---
## My question
So, it is a step-by-step process, right? CPU actually sends an address in binary which contains both row and column info (first 4 bits are row info and the last 4 bits are column), decoder extracts row info and activates the requested column. So, it is connected to data bus as input and all the RAM rows as the output. Then, all columns of that specific row get activated and becomes read enabled. All columns try to send data but multiplexer allows data transmission to the Data bus for only requested column(I am confused how multiplexer knows which column to select). So multiplexer has all columns of a row as inputs and the data bus wire as output.

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: You’ve got the overall flow down perfectly! 🌊 It is indeed a step-by-step dance between the address bits and the hardware components. 💃*
>
> *To clear up the confusion about the **Multiplexer** (MUX) 🚦: it knows which column to select because it receives the **exact same address bits** that the CPU sent out.*
>
> *Let's look at the "Address Breakdown" for your 256-bit (16x16) example using an 8-bit address:*
>
> |**Bits**|**Purpose**|**Destination**|
> |---|---|---|
> |**First 4 Bits** (`1100`)|**Row Address**|The **Decoder** 🗝️|
> |**Last 4 Bits** (`1000`)|**Column Address**|The **Multiplexer** 🚦|
>
> ### *How the Multiplexer "Decides"*
>
> *The Multiplexer is physically wired to those last 4 bits of the address bus. Inside the MUX, there is logic very similar to a decoder. It uses those bits to "open the gate" for only one specific input line.*
>
> *So, if the last 4 bits are `1000` (binary for 8), the MUX internally aligns itself so that only the signal coming from **Column 8** is allowed to pass through to the output wire (the Data Bus). All other columns are effectively blocked or ignored by the MUX's internal switches. 🚫*
>
> ### *Putting it Together*
>
> 1. ***The Decoder** uses the first half of the address to "shout" at an entire **Row** to wake up. 📢*
>
> 2. ***The Multiplexer** uses the second half of the address to "listen" to only one specific **Column**. 👂*
>
>
> *Does it make sense how the address is essentially "split" to control two different parts of the selection process?*
