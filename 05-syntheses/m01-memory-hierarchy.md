---
type: synthesis
module: 1
question: "Compare the memory hierarchy - register, L1-L2-L3 cache, RAM, SSD, HDD — what each is for, rough speed and cost differences, why we have multiple levels."
score:
last_recalled:
---
Here’s how the [[Memory Hierarchy|memory hierarchy]] works, from fastest to slowest:

- [[Register|Registers]]: the tiniest, fastest, and most expensive memory component. The [[CPU]] stores immediate data and instructions in the registers during its fetch–decode–execute cycle.
- [[L1-L2-L3-cache|L1 cache]]: smaller than registers but still very fast. Each CPU core has its own L1 cache, divided into two parts: L1i for instructions and L1d for the data the CPU will work on in the next cycle.
- L2 cache: larger than L1 and sometimes shared between multiple CPU cores.
- L3 cache: the largest of the caches, with hundreds of megabytes, but still faster than [[RAM]].
- RAM (Random Access Memory): the largest volatile memory component. All the memory storage components mentioned so far are volatile, meaning they store data temporarily. Registers and caches store data in [[Latch|latches]] arranged side by side (for example, 64 latches for 64-bit data), but RAM uses a grid layout, which makes it larger and slightly slower to access. RAM is also farther from the CPU, so when the CPU needs to execute a task or work on data, it fetches the data and instructions from RAM and caches them in the cache.
- [[SSD]] (Solid-State Drive) and [[HDD]] (Hard Disk Drive): non-volatile memory devices. SSDs have no moving parts and operate on electrical pulses, while HDDs use magnetic spinning platters and a physical arm to read and write.

We use multiple levels to balance memory speed, efficiency, and cost. For example, HDD is the cheapest but the slowest, while registers are the most expensive but the fastest.


