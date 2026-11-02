#### **WEEK 2 — How the Internet Works**

**Goal:** Understand what happens when a user types `bhara.com` and presses Enter. Every step.

**Stopping criterion:** Walk me through DNS → TCP handshake → HTTP request → server → response → render, in 5 minutes, without notes.

##### Day-by-day

**Day 1 — IP, packets, TCP/UDP (5 hrs)**

- **Watch:** Crash Course CS Ep 28 (Computer Networks), Ep 29 (The Internet).
- **Read:** Kurose & Ross _Computer Networking: A Top-Down Approach_ — Chapter 1 only. (Search "Kurose Ross PDF" — widely available free.)
- **Atoms:** `ip-address.md`, `packet.md`, `tcp.md`, `udp.md`, `bandwidth-vs-latency.md`, `router.md`.
- **Source notes:** `crash-course-cs-ep28.md`, `crash-course-cs-ep29.md`, `kurose-ross-ch01.md`.

**Day 2 — DNS (4 hrs)**

- **Read:** Julia Evans' DNS posts at jvns.ca/categories/dns — first 5 posts.
- **Atoms:** `dns.md`, `dns-resolver.md`, `dns-record.md`, `ttl.md`.
- **Hands-on:** Run `dig google.com`, `dig +trace google.com`, `nslookup bhara.com`. Note what each shows.

**Day 3 — HTTP fundamentals (5 hrs)**

- **Read:** MDN HTTP Overview, MDN HTTP Methods, MDN HTTP Status Codes, MDN HTTP Headers (just the overview pages, not every method).
- **Atoms:** `http.md`, `http-methods.md`, `http-status-codes.md`, `http-headers.md`, `rest.md`, `statelessness.md`, `https.md`, `tls.md`.

**Day 4 — Hands-on with curl (3 hrs)**

- Run all the curl commands from the original Week 2 curriculum (`curl -v https://api.github.com`, etc.).
- Save outputs to `assignments/week-02-curl.md`. Note one thing you learned per command.

**Day 5 — Read "What happens when" (3 hrs)**

- Read the full github.com/alex/what-happens-when document.
- This is the synthesis text — every Week 2 atom should connect to something in this doc.

**Day 6 — Bhara DevTools investigation (3 hrs)**

- Open Bhara in browser → DevTools → Network tab.
- Reload page, log in, do one action.
- For 5 requests, document: URL, method, headers, status, time taken. Save as `assignments/week-02-bhara-network.md`.

**Day 7 — Final check + review (3 hrs)**

- Write the bhara.com walkthrough cold in `assignments/week-02-final.md`.
- Send it to me here.
- Write `08-reviews/week-02-review.md`.