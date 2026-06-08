# What happens when I type `x = 5 + 3` in Python?

```
a = 143, b = 69
c = a + b
```

1. When I hit "Run" on that Python script, the **Python Interpreter** (the translator - the program which transforms python or any human written code to machine/assemble language) looks at `a = 143`. It immediately does a math conversion using combination of logic gates to turn the character "143" into a 64-bit binary string: `0000...10001111`.
2. Then, it asks the **Operating System**, _"Hey, I need a place to put 'a'."_ The OS looks at its **Free List** (its ledger of who owns what). It sees a bunch of addresses that are "free" (meaning they contain garbage from a deleted movie or a closed Chrome tab) and says, _"Use Address 1005."_ The Interpreter makes a **Map** in its head: `a` lives at `1005`.
3. The **Control Unit (CU)** now has to physically save `143`. It takes the address `1005` and splits it. The binary of `1005` tells the hardware: "Go to Bank 1, Row 5, Column 4." - something like that using **Multiplexer**. Then, the CU sends this coordinate to **all 64 grids** (matrices) on the RAM stick at once. It flips the **Write Enable** wire to `1`. This "opens the gates" of the latches at that specific coordinate.
4. The 64 bits of `143` travel down the **Data Bus highway**. Each bit goes to its own matrix, but they all land at `Row 5, Column 4`. The latches flip their gates, and the electricity is trapped. `143` is now "remembered." This repeats for `b = 69` at Address `1006`.
5. When the code hits `c = a + b`, assemble language turns the code into three instructions: 
	- `LOAD a into register A,` 
	- `LOAD b into register B,` 
	- `ADD Register A, Register B.`
6. The Interpreter looks at its map and sees `a` is at `1005`. The CU sends `1005` to the RAM. The RAM's **Multiplexers** find `Row 5, Column 4`. Because **Read Enable** is on, the 64 latches at that spot "shout" their stored charge onto the **Data Bus**. The 64 bits fly into the CPU and get locked into **Register A**. Now the CPU is "holding" the number. It does the same for `b` and puts it in **Register B**.
7. Then, the CU sees the instruction `ADD`. It sends a high-voltage signal to the **ALU** to wake up the **Addition Circuit**. - It opens the "valves" of Register A and B.
    - All 128 bits (64 from each) pour into a massive chain of **64 Full Adders**.
    - In the first adder (the 1s place), the XOR gates calculate the sum and the AND gates calculate the carry.
    - That "carry" bit fast-travels (ripples) to the next adder, and the next, all the way to the 64th bit. This happens in a tiny fraction of a second.
    - The result—`212` in binary—comes out the other side and is caught by the **Accumulator (Register C)**. 
8. It writes back the result in a blank spot of RAM given by the OS. RAM stores the bits in that spot or location in the same way as it stored a and b. When storing a 64 bit number, each bit is stored in a grid but all the 64 bits share the same row and column location.

> ==Updated==: **"math conversion using combination of logic gates to turn the character '143' into a 64-bit binary string."** This conflates two things. The _character string_ `"143"` is already binary in memory (each character as ASCII bytes). When we type the button '1', '4', '3', they already get saved in RAM temporarily as binary. Encoder does this conversion. When we run the program, The interpreter parses this buffer string and produces the _integer_ 143 (a different binary representation). No "logic gate math conversion" — it's a parser walking characters, then standard integer encoding.

>  ==Updated==: **"assemble language turns the code into three instructions."** Python is interpreted, not compiled to assembly. CPython compiles to **bytecode** (`LOAD_FAST`, `BINARY_ADD`, `STORE_FAST`), and the CPython interpreter loop executes those bytecodes — which _eventually_ triggers actual machine instructions, but indirectly.



## Your `Register.md` says registers have 64 input wires and 64 output wires. Your `RAM.md` says RAM has 1 data input wire (per matrix). In your `week-1-question.md` you wrote: _"The 64 bits of `143` travel down the **Data Bus highway**. Each bit goes to its own matrix."_ — **Why is the data bus 64 bits wide if each RAM matrix only has 1 data input wire?** What's actually happening physically?

The data bus is 64 bits wide to store 64-bit binary of "143". Each RAM matrix only store one bit. So, the 64 bits of 143 goes to each matrix in parallel, stores them. So, to store all the bits of a number or letter or anything, we need 64 bit wide RAM.


## What does a CPU actually do per clock cycle?

In short, a CPU executes a command or task per clock cycle. It takes a command from the OS or from the user input/program that is running currently, orchestrates the whole sequence using ALU, RAM, Register via the CU. Thus, in a clock cycle, all of the components remain synchronized and do one single task.

> ==Reviewer Verdict==: Imprecise. A CPU does _one stage of one instruction_ per clock cycle, not "one task." Fetch is one cycle, decode another, execute another.


## What is a register?

Register is the smallest storage component that is fast, relatively small compared to RAM and lives inside the CPU. CPU stores info before and after the task completion in Register.


## What is binary and why do computers use it?

Binary means "2" - two states to represent an information. Computer uses 0 and 1 to represent anything. 0 means off and 1 means on. This is the simplest way to store information in the computer as computer actually stores anything using current flow or voltage. More intermediate states can lead to error as a small change in voltage can also change the information stored. So, for convenience, computers use binary.


## What is a bit? A byte? A kilobyte? Why does this matter for code?

Each of the binary digits - 0 or 1 is called bits. 1 byte = 8 bits. 1 kilobyte = 1024 bytes. This matters for codes because this represents how much of information the program or the computer is holding or using right now. In computer, we measure memory or space using these units. For example, int is 4 bytes long and long is 8 bytes long. So, when we need to store a number in our code, we should be cautious about what data type to choose for it. If the number is too long and we use int data type for it, it can cause integer overflow because of the memory limitation of integer data type.


## What is an interpreter vs a compiler?

Interpreter translates line by line, compiler takes the whole program and translates it at once. So, if interpreter finds any error while translating, it stops there and doesn't execute rest of the program. But compiler does this. Compilers also stop on errors — they just stop at _compile time_ instead of _runtime_. The real difference is _when_ translation happens and whether translation produces a reusable artifact.


