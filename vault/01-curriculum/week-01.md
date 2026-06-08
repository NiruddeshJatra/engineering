# Week 1 — Foundations of Computing


# Day 1-5

## Goal
Watch Crash Course CS Episodes 1-8. Build mental model of what a computer is, from transistors to CPU instructions.

## Stopping criterion
Can explain (without notes, in 5 min): "What happens when I type `x = 5 + 3` in Python and hit enter, from my keyboard down to the CPU and back to screen."

## Episodes
- [x] Ep 1 — Early Computing
- [x] Ep 2 — Electronic Computing
- [x] Ep 3 — Boolean Logic & Logic Gates
- [x] Ep 4 — Registers & RAM
- [x] Ep 5 — The Central Processing Unit
- [x] Ep 6 — Instructions & Programs
- [x] Ep 7 — Advanced CPU Designs
- [x] Ep 8 — Early Programming


## Day 6 — Storage hierarchy + Cache

- **Watch:** Crash Course CS Episodes 19 (Memory & Storage) and 20 (Files & File Systems). Link: same playlist as before.
- **Read:** "Latency Numbers Every Programmer Should Know" — search this exact phrase. It's a famous reference table by Jeff Dean. Memorize the rough orders of magnitude (L1 ≈ 1ns, RAM ≈ 100ns, SSD ≈ 100μs, network ≈ 100ms).
- **Atoms to create:** `cache.md`, `ssd.md`, `hdd.md`, `memory-hierarchy.md`, `latency-numbers.md`.
- **Source notes:** `crash-course-cs-ep19.md`, `crash-course-cs-ep20.md`.

## Day 7 — Operating Systems basics

- **Watch:** Crash Course CS Episodes 18 (Operating Systems) and 21 (Compression).
- **Atoms to create:** `operating-system.md`, `process.md`, `thread.md`, `file-system.md`, `virtual-memory.md`.
- **Source notes:** `crash-course-cs-ep18.md`, `crash-course-cs-ep21.md`.

## Day 8 — Hands-on terminal exercises + Final test (3 hrs)

Run these in your actual terminal (Linux or Mac — if you're on Windows, use WSL):

bash

```bash
ps aux                          # Every running process. Find your terminal in the list.
ps aux | grep python            # Filter to python processes
free -h                         # Linux only — shows RAM usage
df -h                           # Disk usage
xxd /bin/ls | head -20          # The 'ls' program as raw bytes — this is machine code
cat /proc/cpuinfo | head -30    # (Linux only) Your CPU details
```

For each command, write **two lines in `assignments/week-01-terminal.md`**: what you ran, and one observation from the output. No long explanations — this is just to make the abstractions tangible.

**Then the final check:** Write `assignments/week-01-final.md` answering all 6 questions cold.
## Depth tags
#story = know it happened, one line
#medium = know the mechanism at block-diagram level
#deep = must be automatic reflex

## End of week
- [x] 5-minute explanation test
- [ ] Review weak-spots.md
- [ ] Write week-01-review.md