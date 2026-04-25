
#medium 

## Definition
Arithmetic & Logic Unit - the mathematical brain of a computer

## Details
- Can do arithmetic operations like Addition and subtraction. For multiplication and division, series of adders are used.
- Half adder can do addition of two bits. It takes two inputs and outputs the sum and the carry. It can be made using XOR and AND gate.
- Full adder can also take previous adder's carry as input, making it taking 3 inputs and 2 outputs.
- Thus, to make a 8 bit ripple adder, we can use one half adder and 7 full adder.
- In this way, other operations can be done using such combinations of logic gates.
- Engineers came up with a special symbol like "V" to represent an ALU. It takes two 8 bit inputs (can be more bits also), takes a bit operation code to represent what operation to perform and output the result in 8 bit along with some flags like overflow, zero or negative

## Why it matters to me as a dev
Each calculation or logical things we perform in the code eventually goes to the ALU and it performs the operation we asked it for

## Open questions
None

## Links
- Contains: [[Boolean Algebra]], [[Logic Gate]], [[CPU]]
- Source: [[crash-course-cs-ep05]]