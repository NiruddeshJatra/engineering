---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---

## Definition

[[Multitasking & Context Switching]] required allocating memory blocks to different [[programs]] simultaneously. To simplify this for developers, the [[Operating System]] virtualizes memory so each program operates as if it has a continuous block starting at address 0, even if the physical memory is actually scattered in different locations.

## Details

- The OS lies to your application. It gives every running [[process]] the illusion that it owns a single, massive, continuous block of memory starting at address 0. In reality, the OS chops up physical RAM into chunks called "pages" and scatters your data across fragmented physical locations, or even swaps it to the hard drive if [[RAM]] is full.
- The OS logically isolates programs from each other. This ensures that if one program crashes or acts maliciously, it cannot overwrite or steal data from the memory of other programs.

## Why it matters to me as a dev

- Memory Protection ensures Process A cannot look at or overwrite the memory of Process B. When your code tries to access an invalid memory address outside its allocated virtual space, the OS steps in and kills it. This is exactly what a **Segmentation Fault (Segfault)** or a sudden **Out of Memory (OOM) Kill** in a Docker container is.

## Open questions
Understood nothing, I mean, I tried to understand how this works from Youtube but seemed to hard to understand

## In my words
To solve problems created by allowing programs direct access to memory like security, fragmentation, not enough memory, OS virtualizes memory. Now, each program has its own virtual memory and this memory address is mapped to the physical memory via a page table.