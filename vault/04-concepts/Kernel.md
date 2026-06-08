---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---

### Definition

The absolute core, innermost engine of the [[Operating System]] that has direct, unrestricted access to the physical hardware ([[CPU]], [[RAM]], devices).

### Details

- **The Kitchen:** If the OS is a restaurant, the Kernel is the kitchen. It does the actual heavy lifting behind the scenes. The UI and file manager are just the waiters out front.
    
- **User Mode vs. Kernel Mode:** To keep the system safe, the computer splits execution into two modes:
    
    - **User Mode:** Where your standard apps (Chrome, VS Code, your backend API) run. It's a restricted sandbox. Apps cannot touch hardware directly.
        
    - **Kernel Mode:** Reserved for the kernel. Total power. If code crashes here, the entire computer dies (Kernel Panic on Linux or Blue Screen of Death on Windows).
        
- **System Calls:** When your app needs to do something with hardware (like sending a network request or saving a file), it must ask nicely. It pauses, makes a **System Call** to the kernel, the CPU switches to Kernel Mode, the kernel does the work, and then hands control back to your app.
    

### Why it matters to me as a dev

- **The Cost of System Calls:** Switching from User Mode to Kernel Mode isn't free—it requires a hardware context switch that takes time. If your code reads a file byte-by-byte in a giant loop, you are making millions of expensive system calls, which will drag your API performance into the mud. This is why we use **buffered reading** (loading a big chunk into memory at once) to minimize kernel trips.
    
- **Container Performance:** This explains why Docker containers are so lightweight compared to Virtual Machines (VMs). A VM bundles an entire guest OS _and its own separate kernel_, which wastes gigabytes of RAM. A Docker container shares the host machine's Linux kernel and just runs isolated user-space apps on top of it.
    

### Open questions
I think I need to know more about Kernel, Operating System, Process, Thread, Memory and performance optimization like topics. I think they are part of system design which I need to learn eventually.

## In my words
Operating System is a software, right? Kernel is the innermost and most important part of that software that has direct unrestricted access to the hardware. External applications can't access hardware directly. They can request to the kernel, system activates kernel mode and the kernel codes executes the task and communicate with the hardware. It's like a safety layer.