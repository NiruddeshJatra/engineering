When we start a [[Programs|program]], the [[Operating System|operating system]] starts a [[Process|process]]. A program is an executable file, and the OS runs the code on the [[CPU]] one instruction at a time.

At any given moment, the OS decides which instruction to execute next. It can pause one instruction and switch to another. A [[Thread|thread]] is a working unit inside a process, and a process can have multiple threads. Threads share the same memory space, but each process has its own memory, so one process cannot easily communicate with another through a variable. It usually needs [[Kernel|kernel]]-level communication or system calls.

The file system is a [[The Directory File|directory file]] that includes:
- file names
- extensions
- permissions
- pointers to the start and end of each file in memory storage

[[Virtual Memory & Memory Protection|Virtual memory]] is something the OS creates for each process so it can have its own virtual memory and does not access physical memory directly.

User space: when we run a program, the code uses a part of the operating system to run. If it needs to talk to hardware, the system switches to kernel mode, and the kernel takes over and executes the code and talks to the hardware.


# The Operating System: The Grand Conductor

If your computer hardware is an orchestra, the **Operating System (OS)** is the conductor. Raw hardware (like the CPU, RAM, and disks) doesn't know how to share. If two programs tried to use the CPU or write to RAM at the same time, they would overwrite each other's data and crash the system. 

The OS is the software that manages your computer's hardware resources and provides a safe environment for your applications to run.

---

## 1. Kernel Mode vs. User Mode

To protect the computer from buggy or malicious programs, the CPU supports different levels of privilege:

* **Kernel Mode (Protected):** The [[Kernel|kernel]] is the core program of the operating system that has unrestricted access to all physical hardware. If code running in kernel mode crashes, the whole computer crashes (the dreaded Blue Screen of Death).
* **User Mode (Restricted):** All normal applications (like your browser, games, and text editors) run in User Mode. They are "sandboxed" and cannot talk to the hardware directly.

### The System Call
If a program running in User Mode needs to access hardware—such as reading a file from disk or sending a packet over the internet—it cannot do so directly. It must make a **system call**, which is a formal request to the OS kernel to perform the action on its behalf. The CPU switches to Kernel Mode, the kernel executes the request, and then it switches back to User Mode.

---

## 2. Processes vs. Threads

When you start an app, the OS creates a container for it:

* **Process:** An active program running in memory, isolated from all other programs. Each process has its own private memory space assigned by the OS.
* **Thread:** A single sequence of instructions executing within a process. Think of it as a worker.

### The "House" Analogy
* A **Process** is a house. It has its own address, resources, and space.
* A **Thread** is a person living inside the house. 

A process can have multiple threads (workers) running in parallel. Because they live in the same house, they share the same kitchen (memory) and can easily communicate by looking at the same variables. However, a thread in House A cannot easily see inside House B, keeping programs secure and isolated.

---

## 3. Virtual Memory

To prevent programs from accidentally or maliciously reading and writing to each other's memory, the OS uses **virtual memory**.

**Virtual memory** is a technique where the OS assigns each process a private, simulated range of memory addresses. 

To the application, it looks like it has sole access to a giant, continuous block of memory. In reality, the OS and hardware map those virtual addresses to scattered, physical blocks of [[Compare the memory hierarchy - register, L1-L2-L3 cache, RAM, SSD, HDD — what each is for, rough speed and cost differences, why we have multiple levels.|RAM]] behind the scenes. If a program attempts to access an address outside its virtual map, the OS intercepts it and terminates the program (causing a "Segmentation Fault" or crash).

---

## 4. The File System

On your storage drive, data is just a sequence of billions of binary bits. Without structure, it would be impossible to find anything.

A **file system** is a system-wide database and set of rules that organizes, names, and tracks files on a storage drive.

A file system does not store a file as a single contiguous strip of bytes. Instead, it breaks files down into small blocks (usually 4KB each) and scatters them across the SSD or HDD wherever space is available. The file system maintains a master index table (like FAT on Windows or Inodes on Linux) that acts as a directory, mapping file names to the physical locations of their scattered blocks.