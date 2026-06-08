---
tags:
  - deep
source: "[[crash-course-cs-ep06]]"
---

## Definition
A memory component that can store 8 bit/32 bit/64 bit very temporarily.

## Details
- By connecting output wire back to the input wire, we can make a memory component that can store 1 bit of information. To set when to read from it and when to write new data, we can use one data input wire, one write enable wire with some logic gate to enable or disable them to make a gated [[latch]] - the basic memory component.
- 64 latches are used side by side to make a register. Each latch share a common write enable wire, 64 data input wire and 64 data output wire.
- Register is fast but expensive as it needs a wire for every single bit. It is kept inside the [[CPU]] to load data from [[RAM]] to do instant tasks, the data that the CPU is using right now
- To understand better, look at [[Registers & RAM.canvas]]

## Why it matters to me as a dev
Whenever we write code to do any operation, CPU's control unit first loads the data needed to the Register, making it a necessary memory component

## In my words
Register is the fastest and the smallest memory component. Unlike RAM, exactly one latch is used to store 1 bit, not a matrix. It is kept inside CPU to hold instant needed data like operation code, data inputs with which mathematical operations to perform.