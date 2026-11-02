---
type: question
module: 1
status: answered
asked: 2026-04-27
ai: gemini
source:
concepts: []
follows: "[[q-ram-vs-buffer]]"
---
## My question
One final clarification: does parsing only happens when we run the program?

## What I now understand
TODO (Nasif): write this from memory

## Still unclear

## AI explanation
> [!quote]- Raw answer (collapsed)
> *A: It depends on the language, but since we've been using **Python** as our example, parsing happens **right when you run the program**.*
>
> - ***The Workflow:** When you type `python myscript.py`, the interpreter starts reading your text file from the top. It parses the characters `'1'`, `'4'`, and `'3'` into the integer `143` before it ever tries to perform the addition.*
>
> - ***Efficiency:** It does this so that if you use that variable `a` multiple times, it doesn't have to re-parse the text every time; it already has the 64-bit binary number ready in a memory location. 🏃‍♂️*
