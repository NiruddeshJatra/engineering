A [[Programs|program]] file can be stored on disk (such as an [[SSD]] or [[HDD]]), but when it runs, it is loaded into [[RAM]] for faster execution and is executed from volatile memory.

### Correct Version from Claude
on disk, an executable is a file in a structured format (ELF on Linux, PE on Windows) — header + code segment + data segment + metadata. When you run it, the OS: (a) creates a new **process** with its own virtual memory space, (b) loads code → read-only executable region, (c) loads initialized data → read-write region, (d) sets up a **stack** for function calls + a **heap** for dynamic allocation, (e) resolves dynamic library dependencies, (f) jumps to the entry point. The same disk file can be loaded multiple times → multiple independent processes, each with its own virtual memory.


# From File to Process: How a Program Runs

What actually happens when you double-click an application icon on your desktop? How does a dormant file stored on your hard drive turn into an active, running application that you can interact with?

Understanding this requires looking at the boundary between your storage drive and your active memory.

---

## The Analogy: The Cookbook vs. The Baking Process

To understand the difference between a program on disk and a program in memory, think of a kitchen:
* **Program on Disk (The Recipe):** A printed recipe in a cookbook on your bookshelf. It is static, inert, takes up no workspace in your kitchen, and doesn't actually produce any food on its own.
* **Program in Memory (The Baking):** The active process of baking the cake. The chef (CPU) is following the instructions, utilizing counter space (RAM), and mixing ingredients (data).

---

## 1. The Program on Disk: A Static Executable

On your storage drive ([[Compare the memory hierarchy - register, L1-L2-L3 cache, RAM, SSD, HDD — what each is for, rough speed and cost differences, why we have multiple levels.|SSD or HDD]]), a program is stored as an **executable file** (like `.exe` on Windows or ELF on Linux), which is a packaged file containing structured instructions and resource data.

An executable file contains:
* **Header:** Metadata about the program (e.g., is it 64-bit? Where does the code start?).
* **Code Segment:** The raw machine-code instructions for the CPU.
* **Data Segment:** Pre-defined values (like text strings or default configurations).

This file sits quietly on your drive, consuming zero CPU power and zero RAM.

---

## 2. The Program in Memory: An Active Process

When you run the program, the [[Describe what an OS does - process, thread, file system, virtual memory, kernel vs user space|Operating System]] steps in to transform this static file into an active, running **process**. It follows a strict sequence:

### Step A: Sandbox Creation
The OS allocates a brand-new, isolated virtual memory space for the program. This ensures the program cannot touch or corrupt other running applications.

### Step B: Loading Code and Data
The OS reads the executable file from disk and copies its contents into your system's RAM:
* The **Code Segment** is loaded into a read-only region of RAM (so the program cannot accidentally overwrite its own code).
* The **Data Segment** is loaded into a read-write region of RAM.

### Step C: Setting up Stack and Heap
The OS allocates two dynamic memory regions in RAM for the process to use while running:
* **The Stack:** A structured, fast-access memory region used automatically by the CPU to track function calls, keep track of where to return, and store temporary local variables.
* **The Heap:** A large, flexible memory pool where the program can request extra memory dynamically at runtime (for example, when a browser tab loads a new image).

### Step D: Resolving Dependencies
Many programs rely on shared system files called dynamic libraries (like `.dll` files on Windows). The OS locates these libraries in storage and links them into the process's memory space.

### Step E: Jumping to the Entry Point
Finally, the OS modifies the CPU's [[Describe one full fetch-decode-execute cycle, what each stage does and what synchronizes them|Program Counter (PC)]] register to point to the program's starting instruction. The CPU takes over, initiating the fetch-decode-execute cycle, and the program is officially running.

---

## Multiple Processes from One File
Because the file on disk remains unchanged, you can load the same program multiple times. For example, if you open three Google Chrome windows, the OS creates three independent processes. Each process has its own isolated virtual memory space (stack, heap, and data segments) in RAM, but they all run instructions loaded from the same static file on your disk.