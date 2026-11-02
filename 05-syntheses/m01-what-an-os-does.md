---
type: synthesis
module: 1
question: "Describe what an OS does - process, thread, file system, virtual memory, kernel vs user space"
score:
last_recalled:
---
When we start a [[Programs|program]], the [[Operating System|operating system]] starts a [[Process|process]]. A program is an executable file, and the OS runs the code on the [[CPU]] one instruction at a time.

At any given moment, the OS decides which instruction to execute next. It can pause one instruction and switch to another. A [[Thread|thread]] is a working unit inside a process, and a process can have multiple threads. Threads share the same memory space, but each process has its own memory, so one process cannot easily communicate with another through a variable. It usually needs [[Kernel|kernel]]-level communication or system calls.

The file system is a [[The Directory File|directory file]] that includes:
- file names
- extensions
- permissions
- pointers to the start and end of each file in memory storage

[[Virtual Memory & Memory Protection|Virtual memory]] is something the OS creates for each process so it can have its own virtual memory and does not access physical memory directly.

User space: when we run a program, the code uses a part of the operating system to run. If it needs to talk to hardware, the system switches to kernel mode, and the kernel takes over and executes the code and talks to the hardware.


