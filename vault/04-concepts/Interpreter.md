---
tags:
  - story
source: "[[crash-course-cs-ep07]]"
---
## Definition
A [[Programs]] that executes source code line-by-line on the fly, translating it to machine code right as it runs, instead of pre-compiling the whole file beforehand.

## Details
- - **Action:** It reads Line 1 ➡️ translates it to machine code ➡️ runs it immediately. Then moves to Line 2. 🏃‍♂️
    
- **The Catch:** It is inherently slower during execution. If you have a loop that runs 1,000 times, the interpreter stupidly re-translates those exact same lines of code 1,000 times.
    
- **No Build Artifacts:** You don't get an `.exe` or a binary file out of it. You just feed the raw script directly to the interpreter every single time you want to run it.

## Why it matters to me as a dev
- **The Feedback Loop:** This is why languages like Python, Ruby, or standard JavaScript let you modify code and test it instantly. There is no annoying "waiting for code to compile" phase that breaks your focus.
    
- **Late-Crashing Code:** Because it runs line-by-line, an interpreted script will happily execute lines 1 through 49, mutating state or hitting databases. If you have a stupid syntax error on line 50, it won't crash until it actually reaches line 50 at runtime. A [[compiler]] would have caught that and refused to run a single line.

## In my words
While running a program, it executes/translates to machine code each line one by one and stops immediately if it finds any error.