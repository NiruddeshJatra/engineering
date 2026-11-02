---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
follows: "[[q-decoder-fault-and-encoder-meaning]]"
---
## My question
I don't know the answer. But I am now getting confused between multiplexer and decoder. Both pieces job look the same.

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: It is very common to confuse the two because they both use **binary addresses** to control the flow of information! 🧭*
>
> *Let's look at the "direction" of the data to see the difference. 🛣️*
>
> ### *The Decoder (The Address Specialist) 🔍*
>
> *Think of a **decoder** like a specialized switch. It takes a small binary number (the address) and "decodes" it to turn on exactly **one** output wire.*
>
> - ***Input:** A binary address (like `10`).*
>
> - ***Output:** One specific physical line becomes "Active" (like Row 2).*
>
> - ***Role in Memory:** We use this to select which specific row of latches we want to talk to.*
>
>
> ### *The Multiplexer (The Data Selector) 🚦*
>
> *A **multiplexer** (or MUX) is like a "Many-to-One" selector. It has many data input lines, but only **one** data output line. It uses an address to decide which input gets through to the output.*
>
> - ***Input:** Many data streams + one address.*
>
> - ***Output:** The data from the chosen stream.*
>
> - ***Role in Memory:** If we have 16 columns of data but only 1 data bus wire, the MUX picks the bit from the specific column we asked for and puts it on the bus.*
>
>
> |**Feature**|**Decoder 🗝️**|**Multiplexer 🔀**|
> |---|---|---|
> |**Primary Goal**|Selecting a location.|Selecting a data source.|
> |**Outputs**|Many (one active at a time).|Exactly one.|
> |**Example**|Finding a house based on an address.|Picking which TV channel to watch.|
>
> *---*
>
> ### *What happens when things break? 💥*
>
> *Back to your earlier question: if a **decoder** glitched and activated **two rows** at once, it would be like two people trying to shout through the same megaphone at the same time. 📢📢*
>
> *If Row A has a `1` (High Voltage) and Row B has a `0` (Low Voltage), and they are both connected to the same output wire, the electricity essentially "clashes." This creates an unstable voltage that isn't quite a 1 or a 0.*
>
> *If the CPU tries to read that "clashed" voltage, what do you think that might do to the result of our $143 + 69$ calculation? 📉*
