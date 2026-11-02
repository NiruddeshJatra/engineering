---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
follows: "[[q-encoder-decoder-relation]]"
---
## My question
I can't imagine what can happen. Can you tell me? Also, I have learned that encoders convert human readable code to binary, so they are used when we type something or give computer an input. Decoders do the opposite. They turn binary to human readable format, so they are used when we need to output or display something on the screen. Am I right? If that is so, I haven't been able to make connection with what you said about decoder.

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: If a **decoder** accidentally activates two rows at the same time, it’s a bit like two people trying to talk over the same phone line ☎️.*
>
> *Since each latch in a row is connected to a shared **Data Bus** wire, both latches would try to push their voltage onto that same wire. If one latch is a `1` (High Voltage) and the other is a `0` (Low Voltage), they effectively fight each other. This creates "electrical noise" or a short circuit, and the CPU receives corrupted data—a nonsensical blur of bits instead of a clean `1` or `0` ⚡.*
>
> ### *Clarifying Encoders and Decoders*
>
> *You are partially right about how they handle human-to-binary translation, but in computer architecture, these terms also describe a more fundamental "path-finding" job. 🗺️*
>
> 1. ***Encoders (Data Compression):** You're right that when you press a "K" on your keyboard, an encoder turns that specific key-press into a binary code (like `01001011`). It "compresses" many individual inputs into a small binary number.*
>
> 2. ***Decoders (Address Expansion):** While decoders can be used to drive a display (like turning a binary `3` into the specific lights needed to show "3" on a digital clock), their biggest job inside the CPU is **selecting a location**.*
>
>
> *Think of a decoder as a **Switchboard Operator** 🎧. It takes a binary "Address" (like `10`) and uses it to turn on **one specific physical wire** (like the wire for "Row 2").*
>
> ### *The Connection*
>
> *In our memory discussion, the **decoder** is the "GPS" for the RAM grid:*
>
> - *The CPU sends a binary address (e.g., `1005`).*
>
> - *The **Decoder** "decodes" that number and physically energizes the **one specific wire** leading to Row 5, Column 4.*
>
> - *This "opens" only those latches so we can read or write to them.*
>
>
> *Without the decoder, the binary address `1005` would just be a static number; the decoder is what turns that number into a physical action (opening a specific door) 🚪.*
>
> *Since we've seen how the decoder selects a "row," how do you think the computer handles a situation where we want to read a much larger chunk of data than just one 64-bit word at a time? 📚*
