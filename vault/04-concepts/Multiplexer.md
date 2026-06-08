---
tags:
  - medium
source: "[[crash-course-cs-ep07]]"
---
## Definition
A component that selects one of many input signals and forwards it to a single output line.

## Details
- [[CPU]] sends memory address to the [[RAM]]. [[Decoder]] extracts row information and activates that row of the RAM bank. Multiplexer extracts column address from the input and sends only that column's [[latch]]'s info back to the CPU.

## Why it matters to me as a dev
After lots of discussion with Gemini, I finally understood the distinction between Decoder and Multiplexer. 

## In my words
Multiplexer can receive multiple inputs and an address input from CPU. Then, it can activate or output just one column or latch from all the rows and the address.