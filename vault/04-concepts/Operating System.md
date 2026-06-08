---
tags:
  - medium
source: "[[crash-course-cs-ep18]]"
---

### Definition

The master software that boots up first and sits between your code and the raw hardware, managing resources so you don't have to write code to talk to physical electronics.

### Details

- **The Middleman:** If you didn't have an OS, writing a simple "Hello World" app would mean writing custom code to send exact voltages to the screen pixels and manually spinning up the hard drive. The OS handles all that grunt work.
    
- **Resource Traffic Cop:** It decides which app gets CPU time, cuts up your physical RAM so apps don't overwrite each other, and manages permissions (who can read what file).
    
- **What's inside:** It's a bundle of things. It includes the core hardware manager (the kernel), the file system, network stacks, and the user interface (like the windows and desktop you see).
    

### Why it matters to me as a dev

- **The Magic of Abstraction:** When you write `open('config.json')` in Node or Python, you don't care if the user has a cheap HDD or a super-fast SSD. The OS abstracts that away. Your code talks to the OS API, and the OS figures out how to talk to the specific hardware.
    
- **Environment Headaches:** This is the root cause of the "it works on my machine" nightmare. Windows uses backslashes for file paths (`\`), while Linux/Mac use forward slashes (`/`). Linux is case-sensitive with file names, but Mac usually isn't.
    
- **The Reason for Docker:** Since different OSes handle things differently, Docker became standard. It packages your app with just enough of a Linux OS environment so it runs exactly the same on your local machine, a staging server, or AWS.
    

## In my words
Operating System is the master software that boots up when we start the device and manage all the inner complications like memory management, file management, communicating with hardware. Each OS handle things differently and so Docker packages our app in the same Linux OS environment so it run exactly the same on my machine and the cloud server.