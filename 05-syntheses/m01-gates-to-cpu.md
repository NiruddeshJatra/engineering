---
type: synthesis
module: 1
question: "Trace gates → CPU - how gates combine into an ALU, how flip-flops combine into registers and RAM"
score:
last_recalled:
---
The [[CPU]] (Central Processing Unit) is the brain of a [[Computer|computer]]. Inside it, there is a unit called the [[ALU]] (Arithmetic Logic Unit) that performs calculations and logic. The ALU is made from smaller components: full adder, subtractor, Boolean logic circuits, and [[Logic Gate|logic gates]] that are combined to build these computational components.

For example, you can use an XOR gate and an AND gate to build a full adder, which can perform additions. That is how gates combine to make an ALU. Flip-flops are also made from logic gates, and they are the smallest components that can act as memory. You can use 64 of them to build a 64-bit [[Register|register]] inside the CPU to store data and instructions that are needed immediately, or you can store data temporarily in [[RAM]].

In a register, these [[Latch|latches]] are designed in a side-by-side arrangement, but in RAM they are arranged in a grid. [[Decoder|Decoders]] and [[Multiplexer|multiplexers]] are active in the latch that the control unit requires.


