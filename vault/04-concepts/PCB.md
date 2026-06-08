---
tags:
  - medium
source: "[[crash-course-cs-ep17]]"
---
### Definition

The rigid, mass-manufactured board (usually green) that holds electronic components and [[IC]]s in place, connecting them using copper tracks etched directly into the board instead of loose wires.

### Details

- **The Analogy:** If ICs are the individual buildings/factories, the PCB is the entire city map and the paved roads connecting them. 🗺️
    
- **Goodbye Tangled Wires:** Before PCBs, if you opened a radio or a computer, it looked like a plate of spaghetti. If one wire wiggled loose, the whole thing died.
    
- **How it's made:** Metal layers are layered onto a plastic base, and then a chemical process etches away the unneeded metal, leaving behind perfect, flat electrical pathways (called traces) for electricity to flow between chips.
    

### Why it matters to me as a dev

While you won't be designing PCBs as a software dev, this hardware layer is the physical reason why we need [[Device Drivers]] in our operating systems. When your code sends a command to print a document or read from a USB drive, that electrical signal physically travels along the copper traces of a PCB to reach that specific peripheral chip. Drivers are the software translation layer that knows how to map your logic to these physical PCB layout connections.

## In my words
PCB is the green board what we can see if we open up any modern electric machine. every other IC and hardware are connected to PCB. It has wires etched onto it. So, no issue of tangling wire, just plugging in components.