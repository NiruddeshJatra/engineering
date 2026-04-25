
#deep  

## Definition
The component that executes instructions by repeatedly fetching from memory, decoding them and then running them. 

## Details
- While running a program, the program instruction is kept at a memory address and a instruction address register fetches the instruction from the RAM to an instruction register. The fetched data can have two parts: opcode - the code which tells CPU which operation to run, memory address of RAM or Register, the place from where to fetch or write data. Then, it decodes the opcode via its control unit and executes the operation.
- It contains registers for temporary storage, an ALU for math/logic, and a control unit that sequences everything using a clock.
 
## Why it matters to me as a dev
Every line of my Python/JS code eventually becomes CPU instructions. Understanding this means I can reason about why some code is slow or what is happening under the hood.

## Open questions
None

## Links
- Contains: [[Register]], [[RAM]], [[ALU]], [[Clock]]
- Source: [[crash-course-cs-ep07]]