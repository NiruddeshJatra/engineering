---
tags:
  - medium
source: "[[crash-course-cs-ep07]]"
---
## Definition
A component that activates a specific row of the [[RAM]] bank grid by taking extracting memory address input from the [[CPU]].

## Details
- Decoder is the specific circuit that takes binary address and activates exactly **one** output wire (like selecting one specific row in a memory matrix). Without decoders, the CPU couldn't pick a specific [[latch]] to talk to.
- **Input:** It takes the **first 4 bits** from the CPU. 📥
- **Output:** It has **16 output wires**, each connected to a different **Row** in the grid. 📤
- **Action:** It "decodes" that 4-bit number and sends electricity down exactly **one** of those 16 wires. This "wakes up" every latch in that specific row.

## Why it matters to me as a dev
After lots of discussion with Gemini, I finally understood the distinction between Decoder and Multiplexer. 

## In my words
Decoder has 4 inputs, and 16 output wires each connected to a row of the RAM grid. Decoder generates or activates only 1 row from the memory address received via [[Data Bus]].