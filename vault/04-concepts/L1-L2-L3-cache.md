---
tags:
  - medium
source: "[[crash-course-cs-ep20]]"
---

### The Hierarchy

- **L1 Cache:** Built directly into individual [[CPU]] cores. Blazing fast (under 1 nanosecond latency). It is tiny (usually 32KB to 64KB) and split into two specialized pools: **L1i** (for instructions) and **L1d** (for data).
    
- **L2 Cache:** Slightly larger (typically 512KB to 2MB) and slightly slower than L1. It is usually dedicated to a specific core or shared between a pair of cores.
    
- **L3 Cache:** A massive pool (tens to hundreds of MBs) shared across _all_ cores on the CPU die. It is the slowest of the caches but still orders of magnitude faster than system [[RAM]].
    

### The Action

1. When the CPU executes an instruction requiring data, it checks L1.
    
2. If the data isn't there (**Cache Miss**), it looks in L2, then L3.
    
3. If it misses all three layers, the CPU stalls (idles) while it waits hundreds of cycles to fetch the data from RAM.
    
4. When data _is_ fetched from RAM, the memory controller doesn't just pull the single requested byte; it pulls a continuous 64-byte chunk called a **Cache Line** into the [[cache]], gambling that you will need the adjacent bytes next.

### Why it matters to me as a dev
Your code's performance is often dictated more by cache hits than by algorithmic complexity ($O(N)$). If your data layout forces the CPU to bypass L1/L2/L3 and go all the way to RAM, your execution speed drops off a cliff.

## In my words
So, there are 3 kinds of caches which also follow [[Memory Hierarchy]]. L1 is the tiniest cache built directly into CPU cores. L2 and L3 are the larger and slower brothers.