Let’s walk through what happens when we type `print 3 + 4`.
The Python [[Interpreter|interpreter]] stores it in [[RAM]] as strings, not as meaningful values. When the [[Programs|program]] runs, a parser reads those strings and runs [[ALU]] operations to turn them into usable values, like the integers 3 and 4.
After that, they are loaded into the [[Cache|cache]]. If the [[CPU]] finishes its current work, they are loaded into the instruction [[Register|registers]]. This is called fetching. In one CPU cycle, it fetches the instructions and the data and loads the registers. In the next cycle, it decodes what the instructions mean, what the data means, and what to do. In the next cycle, it executes the operation. Everything is synchronized by the [[Clock|clock]], which sends pulses billions of times a second, and each operation or phase (fetch, decode, execute) happens in each clock pulse.


# The Fetch-Decode-Execute Cycle: The Heartbeat of Computing

A computer processor does not run a complete software application all at once. Instead, it breaks programs down into tiny, low-level binary commands called **machine code** (such as "add these two numbers" or "move this value to a register").

The processor executes these instructions one by one in a continuous loop called the **Fetch-Decode-Execute Cycle** (also known as the instruction cycle).

---

## 1. The Conductor: The System Clock

Every step in the instruction cycle is coordinated by the [[Clock|system clock]]. The clock is an electronic oscillator that sends out regular, microscopic electrical pulses billions of times a second. 

The speed of this clock is measured in Gigahertz (GHz). A 3.0 GHz processor has a clock that pulses **3 billion times every second**. Every single micro-operation inside the CPU is synchronized to trigger on these pulses, ensuring that signals don't clash or arrive out of order.

---

## 2. The Three Stages of the Cycle

Let's look at how the CPU processes a single instruction:

```
    +-------------------------------------------------+
    |                                                 |
    v                                                 |
[ FETCH ] ----(Pulse)----> [ DECODE ] ----(Pulse)----> [ EXECUTE ]
```

### Stage 1: Fetch (Get the Instruction)
The CPU needs to retrieve the next instruction from memory.
* The CPU has a special register called the **Program Counter (PC)**, which stores the memory address of the next instruction to be run.
* The CPU reads the address in the PC, fetches the binary instruction from [[Compare the memory hierarchy - register, L1-L2-L3 cache, RAM, SSD, HDD — what each is for, rough speed and cost differences, why we have multiple levels.|RAM or cache]], and loads it into another internal slot called the **Instruction Register (IR)**.
* Immediately after fetching, the PC is incremented (updated) to point to the next instruction's address in memory.

### Stage 2: Decode (Understand the Instruction)
Now that the instruction is inside the CPU, the CPU needs to figure out what it means.
* A circuit inside the CPU called the **Control Unit** acts as the brain's coordinator. It reads the binary pattern inside the Instruction Register.
* The Control Unit decodes the instruction, splitting it into:
  * **Opcode (Operation Code):** The action to perform (e.g., `0001` might mean `ADD`).
  * **Operands:** The targets of the action (e.g., which registers contain the numbers to add, or where to store the result).

### Stage 3: Execute (Run the Instruction)
With the instruction decoded, the Control Unit activates the necessary internal pathways to perform the work.
* If the operation is mathematical, the Control Unit routes the numbers to the [[Trace gates → CPU - how gates combine into an ALU, how flip-flops combine into registers and RAM|ALU (Arithmetic Logic Unit)]] to perform the addition.
* If the operation is moving data, the Control Unit routes data between registers or RAM.
* Once execution is complete, the cycle repeats immediately on the next clock pulse, fetching the next instruction pointed to by the Program Counter.

---

## 3. High-Level Code vs. CPU Cycles

A common source of confusion is mixing up how high-level software (like Python) runs with how hardware works. 

If you type `print 3 + 4` in Python:
* **The CPU does not fetch `print 3 + 4` into its registers.** The CPU cannot read Python.
* Instead, a pre-compiled software program called the [[Explain the difference between compiled and interpreted languages, and what a compiler actually does.|Python interpreter]] is already running on the CPU, executing its own cycle of machine-code instructions.
* The interpreter program reads your string `"print 3 + 4"` from RAM, parses it, calculates the result (`7`), and calls operating system functions to display `7` on the screen.
* The hardware CPU only sees the machine-code instructions of the *interpreter*, executing them one clock pulse at a time.