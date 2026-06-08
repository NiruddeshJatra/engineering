---
tags:
  - medium
source: "[[crash-course-cs-ep06]]"
---

## Definition
The core memory component that can store 1 single bit

## Details
- By connecting output wire back to the input wire, it is possible to hold on to a signal. Adding some more logic gate combination to it enables it to control when to store a data, when to overwrite it.
- Thus, in a gated latch, there is one data input wire, ore write enable wire, one output wire

## Why it matters to me as a dev
How on earth engineers thought of connecting output back to input to store information! THIS IS MIND BLOWING!!!

## In my words
Latch is used in [[Register]], [[RAM]], [[Cache]] and every other memory component as a building block. Two NAND or NOR [[Logic Gate]] are used to build a basic latch.
