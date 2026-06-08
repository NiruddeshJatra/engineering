---
tags:
  - medium
source: "[[crash-course-cs-ep20]]"
---
### Definition

Two ways of finding data: either reading through everything line-by-line in a fixed order (Sequential) or jumping straight to any exact spot instantly (Random).

### Details

- **Sequential Access:** Think of an old cassette tape. If you want to listen to the 5th song, you have to physically fast-forward through songs 1, 2, 3, and 4. This causes massive waiting time (latency). 📼
    
- **Random Access:** Think of a CD or Spotify. You click track 5, and it jumps there instantly. Modern [[RAM]] and [[SSD]]s do this using electrical addresses to read any block of data at any time.
    
- **The Mechanical Problem:** Old hard drives ([[HDD]]s) have a physical arm that has to slide around a spinning disc to find data (called "seek time"). SSDs have zero moving parts, so they can do random access almost instantly. ⚡
    

### Why it matters to me as a dev

This changes how our code performs. If you loop through an array sequentially, the system loves it because it can predict what's coming next and pre-load it. But if you code something that forces the system to do random lookups all over the place (like jumping through scattered pointers), everything slows down because the hardware has to keep searching and jumping around memory.

## In my words
Array is sequential memory: everything is stored in a fixed order, so it is easy to find. But pointers are random access, hardware has to keep searching around memory and it results in slow read speed. RAM and SSD can random access using electrical addresses but HDD needs to go sequentially.