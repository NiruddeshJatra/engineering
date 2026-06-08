---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---
### Definition

A lightweight execution unit (a "worker") that runs _inside_ a [[process]]. A single process can spawn multiple threads to do different tasks at the same time.

### Details

- **Shared Space:** Unlike processes, all threads inside the same process share the exact same memory sandbox (the heap, global variables).
- **Private Stacks:** While they share the main memory, each thread gets its own tiny private runway (a stack) to track which function it is currently executing and its local variables.
- **Lightweight:** Switching between threads is way faster than switching between processes because the [[CPU]] doesn't have to swap out the entire memory map—it just switches execution contexts.

### Why it matters to me as a dev

- **The Nightmare of Race Conditions:** Because threads share the same memory, they can trip over each other. If Thread 1 and Thread 2 try to increment the exact same database connection counter variable at the exact same millisecond, the data gets corrupted. This is the entire reason we have to deal with complex concurrency tools like Mutexes, Locks, and Thread-Safe data structures.
- **UI Responsiveness:** In frontend dev (or mobile apps), the "Main Thread" handles rendering the UI and listening for user clicks. If you run a massive, heavy loop directly on that main thread, the UI freezes because the thread is blocked. You have to push that heavy work onto a background thread (or a Web Worker) to keep the app smooth.

## In my words
A [[process]] can spawn up multiple threads - execution units who share the common memory. This actually enables [[Multitasking & Context Switching]]