# Crash Course CS Ep 18 — Operating Systems

- Watched: 2026-05-29
- Depth target: #medium 

## What this episode covers

- **Batch Processing:** In the 1940s and 50s, programs were manually fed into computers one at a time via punch cards. As processors became exponentially faster, manual loading wasted time, so early OSes were created to automatically process "batches" of programs sequentially without human intervention.
- **Device Drivers:** These standardized APIs or software abstractions allowed programmers to easily interface with different hardware peripherals like printers without needing to know the intricate hardware details of every specific model.
- **Multitasking**
- **Virtual Memory**
- **Memory Protection** 
- **Time-Sharing & Unix:** As multiple users began connecting to mainframes via terminals, OSes like Multics introduced time-sharing to divide a fraction of the CPU's resources among everyone. Because Multics was heavily over-engineered, developers created Unix as a lean alternative. Unix stripped out complex error recovery, opting instead to do intentionally crash the system - triggering a "kernel panic" - when fatal errors occurred.
- **Home Computers:** Early 1980s personal computers used simple OSes like MS-DOS, which lacked protected memory and multitasking to save space. This lack of protection is why a single misbehaving program could crash the entire system, leading to the infamous Windows "blue screen of death".

## What I understood
This episode covered some of the important concepts of OS like multitasking, virtual memory. It covered the evolution and core functions of OS.

## What confused me
Nothing

## Terms I heard but didn't fully get
Nothing

## Link forward (atoms I should create)
- [[Process]]
- [[Thread]]
- [[Kernel]]
- [[Multitasking & Context Switching]]
- [[Virtual Memory & Memory Protection]]