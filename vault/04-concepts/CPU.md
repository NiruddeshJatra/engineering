---
tags:
  - deep
source: "[[crash-course-cs-ep07]]"
---

## Definition
The component that executes instructions by repeatedly fetching from memory, decoding them and then running them. 

## Details
- While running [[programs]], the program instruction is kept at a memory address and a instruction address register fetches the instruction from the [[RAM]] to an instruction [[register]]. The fetched data can have two parts: opcode - the code which tells CPU which operation to run, memory address of RAM or Register, the place from where to fetch or write data. Then, it decodes the opcode via its control unit and executes the operation.
- It contains registers for temporary storage, an [[ALU]] for math/logic, and a control unit that sequences everything using a [[clock]].
 
## Why it matters to me as a dev
Every line of my Python/JS code eventually becomes CPU instructions. Understanding this means I can reason about why some code is slow or what is happening under the hood.

## In my words
CPU - Central processing unit - processes everything that a computer does. To know what to run or process, in every clock cycle, it first looks for instruction in the instruction register. Instruction code is already fetched from RAM to cache to here via [[Data Bus]]. This is the fetch phase. Then, it decodes the opcode stored in information register and processes the inputs stored in other registers. Then, it executes the operation by sending the inputs to the [[ALU]]. Finally, the output gets stored in an output register. That's how, fetch -> decode -> execute happens.