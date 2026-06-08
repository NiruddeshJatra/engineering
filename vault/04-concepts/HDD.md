---
tags:
  - medium
source: "[[crash-course-cs-ep20]]"
---

## Definition

A persistent, non-volatile storage device that uses physical spinning magnetic platters and a moving mechanical arm to read and write data.

## Details

- **The Record Player Analogy:** An HDD is literally a tiny, hyper-precise record player inside a metal box. The platters spin at crazy speeds (usually 7200 RPM), and a tiny read/write head flies micro-inches above the surface to read the magnetic state of the disk.
    
- **The Physics Bottleneck (Seek Time):** To read a file, two physical things must happen: the platter has to spin to the right spot, and the arm has to move to the right track. This mechanical delay is called **seek time**. It takes a few milliseconds, which feels like an absolute eternity to a [[CPU]] that operates in nanoseconds.
    
- **Fragmentation:** If a file's data blocks are scattered all over different parts of the spinning disc rather than in one continuous line, the physical arm has to bounce all over the place to stitch it together, making the drive painfully slow.
    

## Why it matters to me as a dev

- **The Root of Caching:** HDDs are the ultimate justification for why software engineers invented caching layers (like Redis) and in-memory databases. Touching an HDD during a live API request is a performance death sentence for your app.

## Open questions
- Magnetic state? Wtf is this?
- What is spot and track here?
- I still don't understand how HDD and [[SSD]] work

## In my words
A type of non-volatile memory which uses physical spinning magnetic platters and a read and write arm. To read a file, the platter has to spin to the right spot and the arm has to move to the right track, so this needs time. As a result, reading data from an HDD is slower than from SSD.