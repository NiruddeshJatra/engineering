
#deep  

## Definition
A memory component that can store data temporarily - Random Access Memory

## Details
- We can organize latches in a grid and to connect to a specific latch, we can enable the wire of its row and column. Thus, we need to keep track of the row column - memory address of each latch, which we do by a component called Multiplexer. So, in a 256 bit memory, we need 8 bit of memory address, 1 data input wire, 1 read enable wire, 1 write enable wire.
- To store a 64-bit number, we need 64 of these 256-bit matrices side-by-side.
- So, whenever computer needs to store a number, let's say 01100100, all bits go to the memory address (e.g., 00001111), meaning 1st row, 15th column of each matrices
- A 1GB RAM stick contains 8 physical black chips, each of these chips have multiple "banks" - a group of 64 matrices working together
- RAM is slower than Register but cheaper as it needs less wire, but to find a "bit" one needs to have the address of the row and column of that specific bit.
- To understand the breakdown, look at [[Gemini_Generated_Image_px59u1px59u1px59.png]]

## Why it matters to me as a dev
Understanding what separates RAM from Register is an important step towards my learning because it cleared my misconception. I thought Register is just a smaller component of RAM.

## Open questions
None

## Links
- Contains: [[Boolean Algebra]], [[Logic Gate]], [[Register]], [[ALU]]
- Source: [[crash-course-cs-ep06]]