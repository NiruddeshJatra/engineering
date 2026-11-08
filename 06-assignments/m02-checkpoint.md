---
type: assignment
module: 2
written_by: claude
---
# Module 2 checkpoint: Units 1–3, cold

This replaces the mid-module oral checkpoint and the Unit 1 cold checks that were skipped. It decides whether you go to Unit 4 (the no-AI server build) or back to specific notes first.

## Rules

- **Before:** read notes 10–13 ([[m02-10-dns]] to [[m02-13-http]]) and do the "Try it" commands in notes 4–13. Then **wait at least one day.**
- **During:** no notes, no AI, no web search. Dictating with Wispr is fine.
- **Time:** about 75 minutes. Answer every numbered sub-part, even briefly; an unanswered part scores zero.
- **Where:** write your answers in a new file, `05-syntheses/m02-checkpoint-answers.md` (`type: synthesis`, `module: 2`, `written_by: nasif`). Clean up dictation errors before sending it to Claude, so the grade is about your knowledge, not Wispr's.
- **After:** send the file to Claude for grading. Pass mark: 7/10. Corrections follow the structural rewrite rule: rewrite the *whole* answer, not the flagged sentence.

## Part A: terminology drill (2 points, 15 minutes)

One line each. Precise beats long.

1. Message, segment, datagram, frame
2. Protocol
3. Store-and-forward
4. Transmission delay
5. Propagation delay
6. Queueing delay
7. Throughput
8. Forwarding table
9. Routing protocol
10. Subnet mask
11. Default gateway
12. ARP
13. NAT
14. Socket
15. TTL (both meanings)

## Part B: mechanisms (5 points)

**B1. Delay (1 point).** A 1,500-byte packet crosses 3 links. Each link is 10 Mbps and 1,000 km of fibre.
1. Ignoring processing and queueing, what's the total delay? Show the working.
2. Which part dominates?
3. What changes if every link becomes 1 Gbps, and what does that teach you about "faster internet"?

**B2. Before the first frame (1 point).** Your laptop is `192.168.1.20/24` at home and wants to send a request to `bhara.com`.
1. List every address or value the laptop must know before the first frame of the request leaves it.
2. For each: how did it learn it?
3. For each: which header does it go into?

**B3. At a router (0.5 points).** A datagram passes through an ISP router.
1. What does the router change in the datagram and its frame?
2. What does it leave alone?
3. Why?

**B4. TCP (1 point).**
1. Give the two ways a sender learns that a segment was lost.
2. Flow control vs congestion control: what each protects, and how the sender learns each limit.
3. Why can't a program reading from a TCP socket assume one read returns one whole message?

**B5. TLS (1 point).**
1. Where on your laptop does TLS run, and what does the OS's TCP see?
2. What does a certificate prove, who vouches for it, and why does your browser trust them?
3. What can your ISP still see when you visit an HTTPS site?

**B6. HTTP (0.5 points).**
1. How does a receiver know where the headers end, and where the body ends?
2. One problem HTTP/2 solved, one it left, and how HTTP/3 deals with it.

## Part C: design "why" (2 points)

These are the questions you fought hardest over. Answer them in your own words, as if explaining to a reader of your blog.

**C1.** Why can't a laptop's IP address be assigned at the factory, like a serial number? Use your phone-number idea and make it work for you.

**C2.** Give the strongest reason the link layer has its own addresses instead of using IP addresses. Then give one more.

**C3.** Correct this statement, and explain what's wrong with it: *"MAC addresses are used between a device and its router; IP addresses are used between routers."*

**C4.** What do hierarchical addressing, private address ranges and NAT each solve? Why is "NAT fixes hierarchy" wrong?

## Part D: your own traceroute (1 point)

```
  1     1 ms     1 ms     1 ms  192.168.1.1
  2     5 ms     2 ms     4 ms  10.8.66.1
  …
  7    30 ms    13 ms    15 ms  10.198.9.53
  8–13  *        *        *     Request timed out.
 14    13 ms    12 ms    11 ms  216.198.79.1
```

1. What does hop 2 being `10.8.66.1` suggest about your own public address? How would you confirm it?
2. Why can hop 7 show a higher time than hop 14?
3. What do the stars at hops 8–13 mean, and what do they *not* mean?
4. Hop 14 answers in about 12 ms. Use propagation delay to say something about where that machine is.

## Part E: Module 1 recall (ungraded, 10 minutes, overdue)

Cold, no notes:

1. One fetch-decode-execute cycle, ending with why the CPU never fetches a Python line, and what it fetches instead.
2. Why the memory hierarchy works (not just why it exists), with rough latency numbers for a cache, RAM and an SSD.

Your four open Module 1 errors are listed in Notion. This is their spaced-recall check.
