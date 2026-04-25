
#deep  

## Definition
the CPU is a piece of hardware which is controlled by easy-to-modify software

## Details
- **Opcodes and Data:** Instructions are stored in binary memory just like standard data. They consist of an "opcode" (the specific operation to perform, such as `ADD` or `LOAD`) and usually a memory address or register indicating what data to operate on.
- **Control Flow:** Programs execute sequentially until they hit a `JUMP` instruction. Jumps overwrite the instruction address register, forcing the CPU to change its execution order.
- **Conditionals:** A regular jump will create an infinite loop. To evaluate logic and break out of loops, CPUs rely on conditional jumps (like `JUMP_NEGATIVE`), which only execute if a specific ALU flag is triggered.
- **Software Abstraction:** Software can bypass hardware limitations. For instance, if an ALU lacks a dedicated division circuit, a program can use a loop of repeated subtraction and conditional jumps to achieve the same exact result.
- **Instruction Sets:** Early processors, such as the 1971 Intel 4004, had very small instruction sets with only 46 operations. Modern CPUs use larger, variable-length instructions to support thousands of specialized operations.
 
## Why it matters to me as a dev
As a developer, I mainly make software which is nothing but a collection of executable programs. Those programs have instruction sets and CPU executes them. Those instruction can actually use loops by JUMP instruction and conditional like JUMP_NEGATIVE, which is interesting

## Open questions
None

## Links
- Contains: [[Register]], [[RAM]], [[ALU]], [[CPU]]
- Source: [[crash-course-cs-ep08]]