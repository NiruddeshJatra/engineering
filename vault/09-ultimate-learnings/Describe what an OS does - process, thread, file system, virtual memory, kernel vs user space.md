When we start a program, the operating system starts a process. A program is an executable file, and the OS runs the code on the CPU one instruction at a time.

At any given moment, the OS decides which instruction to execute next. It can pause one instruction and switch to another. A thread is a working unit inside a process, and a process can have multiple threads. Threads share the same memory space, but each process has its own memory, so one process cannot easily communicate with another through a variable. It usually needs kernel-level communication or system calls.

The file system is a directory file that includes:
- file names
- extensions
- permissions
- pointers to the start and end of each file in memory storage

Virtual memory is something the OS creates for each process so it can have its own virtual memory and does not access physical memory directly.

User space: when we run a program, the code uses a part of the operating system to run. If it needs to talk to hardware, the system switches to kernel mode, and the kernel takes over and executes the code and talks to the hardware.