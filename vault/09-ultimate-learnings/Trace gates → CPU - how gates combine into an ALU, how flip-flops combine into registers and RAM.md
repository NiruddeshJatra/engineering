The [[CPU]] (Central Processing Unit) is the brain of a [[Computer|computer]]. Inside it, there is a unit called the [[ALU]] (Arithmetic Logic Unit) that performs calculations and logic. The ALU is made from smaller components: full adder, subtractor, Boolean logic circuits, and [[Logic Gate|logic gates]] that are combined to build these computational components.

For example, you can use an XOR gate and an AND gate to build a full adder, which can perform additions. That is how gates combine to make an ALU. Flip-flops are also made from logic gates, and they are the smallest components that can act as memory. You can use 64 of them to build a 64-bit [[Register|register]] inside the CPU to store data and instructions that are needed immediately, or you can store data temporarily in [[RAM]].

In a register, these [[Latch|latches]] are designed in a side-by-side arrangement, but in RAM they are arranged in a grid. [[Decoder|Decoders]] and [[Multiplexer|multiplexers]] are active in the latch that the control unit requires.


# Tracing Gates to CPU: ALUs, Registers, and RAM

In the previous article, we saw how [[Explain how a bit is physically stored - transistor as switch → logic gate → flip-flop|transistors build logic gates]], and how those gates can store a single bit of data. Now, we will see how we scale this up: how we combine gates to perform math, and how we pack flip-flops together to build memory systems like registers and RAM.

---

## 1. Math from Logic: The ALU (Arithmetic Logic Unit)

The **ALU** (Arithmetic Logic Unit) is the mathematical core of the CPU. It is responsible for performing basic calculations (like addition and subtraction) and logical comparisons (like checking if two numbers are equal).

The ALU contains no magic; it is built entirely by combining logic gates.

### How We Build a Binary Adder
Let's see how we add two single-bit binary numbers ($A$ and $B$) using logic gates. 
* In binary:
  * $0 + 0 = 00$
  * $0 + 1 = 01$
  * $1 + 0 = 01$
  * $1 + 1 = 10$ (which is $2$ in decimal)

If you look at the outputs:
* The right-side bit (the Sum) is only `1` if *either* $A$ or $B$ is `1`, but not both. This matches the behavior of an **XOR gate** (Exclusive OR).
* The left-side bit (the Carry) is only `1` if *both* $A$ and $B$ are `1`. This matches an **AND gate**.

By linking an XOR gate and an AND gate together, we create a **Half Adder**:

```
   A -----+-----\__  XOR  \_______ Sum
          |     /         /
   B ---+-|----/
        | |
        | +-----\__  AND  \_______ Carry
        +-------/         /
```

To add larger numbers (like 64-bit numbers), we daisy-chain these circuits together. We add a third input for the "Carry In" from the previous column. This is called a **Full Adder**. By lining up 64 Full Adders side-by-side, we can add two 64-bit numbers in a fraction of a nanosecond.

---

## 2. Fast Memory: Registers

A CPU needs a place to hold the numbers it is actively calculating. It cannot wait for data to travel from your computer's main memory—that would take too long. Instead, it uses **registers**.

A **register** is a small group of flip-flops arranged side-by-side that stores a single piece of data (like a 64-bit number) directly inside the CPU.

To build a 64-bit register, we place 64 flip-flops in a row. They all share the same clock line. When the clock pulse triggers, all 64 flip-flops save their input voltages simultaneously, storing a 64-bit number.

---

## 3. Large Memory: RAM (Random Access Memory)

If we built your computer's main memory (**RAM**) out of registers, it would be extremely fast, but it would also be giant, hot, and cost thousands of dollars. Instead, RAM is designed to prioritize capacity and density.

Instead of lining up flip-flops side-by-side, RAM arranges memory cells in a massive **grid** of rows and columns.

```
                  Column 0      Column 1
                     |             |
   Row 0 (Wordline) -+-[ Cell 0 ]--+-[ Cell 1 ]
                     |             |
   Row 1 (Wordline) -+-[ Cell 2 ]--+-[ Cell 3 ]
                     |             |
```

To read or write to a specific address, RAM uses two key circuits:
1. **Decoder:** A circuit that takes a binary address (like `10` or row #2) and lights up the single corresponding row line (the wordline).
2. **Multiplexer:** A circuit that acts as a selector switch, routing the data from the chosen column line to the output.

### SRAM vs. DRAM: The Grid Trade-off
There are two ways we build cells in this grid:
* **SRAM (Static RAM):** Each cell is made of a latch (about 6 transistors). It is fast and does not need refreshing, but it takes up a lot of physical space. This is used for CPU cache.
* **DRAM (Dynamic RAM):** Each cell is made of just **one transistor and one capacitor** (a microscopic component that stores an electrical charge like a tiny battery). Because it only uses one transistor, DRAM is incredibly cheap and dense (billions of cells on a tiny chip). However, capacitors leak charge, so DRAM must be refreshed (read and rewritten) thousands of times a second to prevent data loss. This is used for your computer's main RAM.