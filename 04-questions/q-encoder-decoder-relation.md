---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
---
## My question
I have read about encoders and decoders. Are they somehow related to what we are discussing now?

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: You hit on two of the most important components in digital logic! They are absolutely related to how that `143` gets into the right "room" in the RAM "warehouse."*
>
> - ***Decoders (The GPS):** Remember how we talked about an address like `1005` being split into rows and columns? A **Decoder** is the specific circuit that takes that binary address and activates exactly **one** output wire (like selecting one specific row in a memory matrix). Without decoders, the CPU couldn't pick a specific latch to talk to.*
>
> - ***Encoders (The Translator):** These do the opposite. They take multiple inputs and compress them into a smaller binary code. For example, when you press a key on your keyboard, an encoder circuit takes that specific physical button press and turns it into the binary pattern (the ASCII/Unicode) that represents that character. ⌨️*
>
>
> *Since we've talked about how a decoder picks a "row" in memory, what do you think would happen if the decoder circuit had an error and accidentally activated **two** rows at the same time when trying to read your data? 💥*
