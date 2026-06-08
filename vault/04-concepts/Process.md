---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---
### Definition

A completely isolated, independent running instance of a [[programs]] that has its own dedicated memory space allocated by the [[operating system]].

### Details

- **The Sandbox:** When you launch an app (like Chrome or a Python script), the OS spins up a process. This process gets its own private chunk of [[RAM]].
    
- **Hard Walls:** Process A cannot see or touch Process B's memory. This is handled by [[Virtual Memory & Memory Protection]]. If one process goes rogue and crashes, it won't take down the other running processes. 🛑
    
- **Heavyweight:** Creating a new process is slow and expensive because the OS has to allocate an entirely new virtual memory space and set up tracking structures.

### Why it matters to me as a dev

- **Node.js vs Django/Gunicorn:** Node.js runs on a single process. If an unhandled error crashes that process, your entire backend goes down for every user until a tool like PM2 restarts it. Django apps often use a WSGI server like Gunicorn to spin up multiple worker _processes_—if one process crashes handling a bad request, the other processes keep serving traffic.
    
- **Inter-Process Communication (IPC):** Because processes are isolated, if you actually need two different processes to talk to each other, you can't just pass a variable. You have to use complex OS-level workarounds like WebSockets, Redis pub/sub, or gRPC.
    

## In my words
So, whenever we run a program, OS allocates some virtual memory for it and runs it in isolation, this is called a process. Starting a program takes time as OS has to allocate memory for it, set up tracking structures with virtual and physical memory. As they run in isolation, two programs can't talk to each other directly as they cannot access each other's process.