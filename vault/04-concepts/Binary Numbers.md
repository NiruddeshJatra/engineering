---
tags:
  - medium
source: "[[crash-course-cs-ep04]]"
---

## Definition
2-base representation of number

## Details
- Multiple states for representing or storing information can be messy in computers as signals can get mixed up by outside influences and a small change in voltage can change the stored information. For example, if 2-3 volt is for storing 2 and 3-4 volt is for storing 3, a sudden accidental change in voltage can change 2 to 3.
- Just like 263 in decimal can be understood as = 10$^2$ * 2 + 10$^1$ * 6 + 10$^0$ * 3; 101 in binary is =  2$^2$ * 1 + 2$^1$ * 0 + 2$^0$ * 1 = 5 in decimal.
- Each of the binary digits - 0 or 1 is called bits.
- 1 byte = 8 bits
- 8 bit/32 bit/64 bit is used to represent numbers of any sort of information in binary
- For representing negative numbers, the first bit is for sign. 1 is for (-), 0 is for (+)
- For representing floating point numbers, in a 32 bit system, the first bit is again for sign, the next 8 bits are to represent the exponent, rest of the bits are to represent the significand. For example: 625.9 can be written as 0.6259 * 10$^3$. Here, 3 is the exponent and 6259 is the significand. It follows IEEE 754 standard

## Why it matters to me as a dev
Understanding how any number can be converted to binary is fascinating

## In my words
[[ASCII & Unicode]] all comes down to this. [[ALU]] also performs conversions using [[Encoder]] for turning keyboard inputs into binary numbers.