---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---
## Definition
[[CPU]]s execute instructions sequentially. Multitasking is largely an illusion created by the [[Operating System]] rapidly swapping which [[process]] gets to use the CPU.

## Details
- To prevent expensive CPUs from sitting idle while waiting for slow mechanical input/output tasks, the 1962 Atlas computer introduced multitasking. The OS could put a program to sleep while it waited for a printer, run another program, and then switch back when the printer finished.

- Swapping processes isn't free. The CPU has to save the current program's state ([[register]]s, pointers) and load the next one. This is called a **context switch**. If you spin up thousands of unnecessary [[thread]]s, the CPU spends more time switching between them than doing actual work—a performance death spiral called "thrashing."

## Why it matters to me as a dev
Meh

## In my words
Multitasking is an illusion, actually CPU execute tasks sequentially. But it can put a process to sleep while waiting for slow output. But this kind of swapping processes has its own drawback - saving current state, loading the next one, spinning up unnecessary threads.