---
tags:
  - medium
source: "[[crash-course-cs-ep07]]"
---
## Definition
A small, ultra-fast memory layer sitting directly next to the [[CPU]] core that holds temporary copies of frequently used data from the [[RAM]] to prevent the CPU from idling.

## Details
- Cache works as a desk tray for the CPU. If CPU needs to fetch instruction, data from the RAM, hundreds of [[clock]] cycle would get wasted for carrying data via [[data bus]] from the RAM to the CPU. Cache stores a big chunk of upcoming instructions and data next to the CPU for faster execution.
- **The Vocabulary to Know:**
	- **Cache Hit:** The CPU finds the data it needs in the cache. Execution is instant.
	- **Cache Miss:** The data isn't in the cache. The CPU stalls and is forced to fetch it from slow RAM.

## Why it matters to me as a dev
- **Hardware Sympathy:** Algorithmic complexity ($O(N)$) doesn't tell the whole story. Your code can be mathematically perfect, but if it forces constant cache misses, it will run incredibly slow.
    
- **Data Layout:** This is the concrete reason why **Arrays** are faster than **Linked Lists** for sequential data. Because arrays are stored in one continuous block of memory, a single fetch loads the entire array (or a huge chunk of it) into the cache at once (Cache Hits). Linked lists scatter nodes randomly across RAM, forcing a slow trip to RAM for every single node pointer you follow (Cache Misses).

## In my words
To avoid wasting lots of clock cycles to fetch data from RAM, a smaller memory component is build next to CPU which holds upcoming instructions and frequently used data. It is slower but larger in size than [[Register]]. There are 3 types of cache:[[L1-L2-L3-cache]]