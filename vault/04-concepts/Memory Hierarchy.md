---
tags:
  - medium
source: "[[crash-course-cs-ep20]]"
---

### Definition

A pyramid ranking system for computer memory where we trade off speed, storage size, and cost to keep the CPU running without waiting.

### Details

- We want memory that is insanely fast, holds terabytes of data, and is dirt cheap. But physics says no. ❌
    
- So, computers cheat by using a layered pyramid:
    
    - **Top (Caches/Registers):** Super fast, sits right inside the CPU, but tiny and expensive.
        
    - **Middle (RAM):** Fast, medium size. It is _volatile_—meaning the second you turn off the PC, everything inside it vanishes. 🔋💨
        
    - **Bottom (SSD/HDD):** Slower, but massive and cheap. It is _non-volatile_, meaning it keeps your files safe even with no power.
        
- The system constantly moves data up the pyramid when the CPU needs it, and pushes it down when saving.
    

### Why it matters to me as a dev

This is the exact reason why we use caching tools like Redis in backend dev. Fetching data from a database on a slow SSD takes forever compared to fetching it from RAM. By keeping hot data in RAM, we don't make the server stall.

## In my words
[[SSD]] and [[HDD]] are non-volatile, the slowest, cheapest and largest storage device. [[RAM]] is volatile and it sits in the middle. [[Cache]] and [[Register]] is super fast, tiny and most expensive. This is the hierarchy to remember and utilize when needed.