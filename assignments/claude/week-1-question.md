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