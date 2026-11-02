---
type: synthesis
module: 1
question: "Explain the difference between a program on disk and a program running in memory"
score:
last_recalled:
---
A [[Programs|program]] file can be stored on disk (such as an [[SSD]] or [[HDD]]), but when it runs, it is loaded into [[RAM]] for faster execution and is executed from volatile memory.

### Correct Version from Claude
on disk, an executable is a file in a structured format (ELF on Linux, PE on Windows) — header + code segment + data segment + metadata. When you run it, the OS: (a) creates a new **process** with its own virtual memory space, (b) loads code → read-only executable region, (c) loads initialized data → read-write region, (d) sets up a **stack** for function calls + a **heap** for dynamic allocation, (e) resolves dynamic library dependencies, (f) jumps to the entry point. The same disk file can be loaded multiple times → multiple independent processes, each with its own virtual memory.


