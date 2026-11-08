---
type: module
module: 2
status: active
written_by: claude
---
# Module 2 — How the Internet works

**Goal:** understand what happens when I type `bhara.com` and press Enter, every step.

**Exit gate:** exam ≥ 7.0/10 **and** a working HTTP server written from scratch with Python's `socket` module only, no AI.

**Notes:** the 13 reading-order notes below were written by Claude at my request (2026-11-08), consolidated from my Kurose notes, my questions to Claude, ChatGPT and Gemini, and the old article drafts. My own questions sit inside them, where they come up. Proof of understanding is the checkpoint and the exam, not the number of notes. Progress lives in Notion, not here.

## Read in this order

| # | Note | Unit |
|---|---|---|
| 1 | [[m02-01-the-internet-as-a-system]] | 1 |
| 2 | [[m02-02-packet-switching-and-delay]] | 1 |
| 3 | [[m02-03-layers-and-encapsulation]] | 1 |
| 4 | [[m02-04-ports-sockets-and-udp]] | 2 |
| 5 | [[m02-05-joining-a-network-dhcp]] | 1 |
| 6 | [[m02-06-ip-addresses-and-subnets]] | 1 |
| 7 | [[m02-07-mac-addresses-and-arp]] | 1 |
| 8 | [[m02-08-public-private-addresses-and-nat]] | 1 |
| 9 | [[m02-09-routing-across-the-internet]] | 1 |
| 10 | [[m02-10-dns]] | 2 |
| 11 | [[m02-11-tcp]] | 2 |
| 12 | [[m02-12-tls]] | 3 |
| 13 | [[m02-13-http]] | 3 |

My own material: [[kurose-ch01]] (book notes) and [[m02-first-flow-summary]] (my first flow summary, with corrections).

## Order of work from here

1. Read notes 10–13 (the parts with the fewest questions in my chats), then do every "Try it" in notes 4–13.
2. Wait a day, then sit [[m02-checkpoint]] cold. Answers go in `05-syntheses/m02-checkpoint-answers.md`.
3. Unit 4: build the server (below).
4. Unit 5: cold trace, then the exam.
5. Only after the exam passes: Module 2 articles (3 max).

## Unit 1 — Layers and packets
- Crash Course CS Ep 28–29
- Kurose & Ross §1.1–1.5
- Notes 1–3, 5–9
- Hands-on (Windows): `ipconfig /all`, `route print -4`, `arp -a`, `ping 8.8.8.8`, `tracert`, `pathping`

## Unit 2 — DNS, TCP, UDP
- Julia Evans' DNS posts (5)
- Kurose §2.4, §3.5
- Notes 4, 10, 11
- Hands-on: `nslookup`, `ipconfig /displaydns`, `dig +trace` (WSL or Git Bash), Wireshark filter `tcp.flags.syn == 1`
- Mid-module checkpoint: [[m02-checkpoint]] (covers Units 1–3)

## Unit 3 — HTTP and TLS
- MDN HTTP overview pages
- hpbn.co Ch 11–12
- Notes 12, 13
- Hands-on: `curl -v`, `curl -I`, `openssl s_client` (Git Bash or WSL), DevTools Protocol column

## Unit 4 — Build, no AI
- Socket-only HTTP server in `06-assignments/m02-http-server/`
- References allowed: **RFC 9112** (HTTP/1.1 syntax; it replaced RFC 7230 in 2022), RFC 9110 (status codes), Python's `socket` docs. No AI, no tutorials.
- Stuck > 30 min → [[weak-spots]]
- Log actual hours against the 6-hour budget.

## Unit 5 — Synthesis
- alex/what-happens-when
- Bhara DevTools (5 requests)
- Cold trace in `05-syntheses/m02-bhara-trace.md`, written before reading the summary table at the end of note 13.

## Parked for a later module
[[later-backend-topics]]: web servers, app servers, caching layers, rendering. Not Module 2.

## Views
![[concepts.base#Module 2]]

![[questions.base#Module 2]]

![[sources.base#Module 2]]
