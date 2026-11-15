---
type: concept
module: 2
unit: 1
order: 9
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 9. Routing across the Internet

*Where we are: the datagram has left your home with its source rewritten by NAT. Ahead of it are a dozen or more routers belonging to several different organisations, none of which has seen it before. How does each one know where to send it?*

## The problem

No router on the Internet knows the whole route from your laptop to the server. There are tens of thousands of independently run networks, links fail and come back, and new networks appear every day. Yet every datagram must be passed, router by router, in roughly the right direction, millions of times per second.

> [!question] You asked (ChatGPT)
> "Again, what is IP? How can it know the destination address? Is router a device which can determine where to forward the packet next from the destination address?"

> [!question] And (Gemini)
> "From the destination IP address, routers can determine where to send the packet, the next hop, correct?"

Yes. IP (*Internet Protocol*) is the network layer's protocol: it defines the datagram format, including the source and destination addresses, and the rules routers follow. The sender writes the destination address into the IP header; it learned it from DNS (note 10). Each router reads only that destination address and chooses the next hop. Nobody plans the whole route in advance. Each router makes one local decision, and the decisions add up to a path.

## Two jobs: forwarding and routing

There are two different jobs here, and confusing them is a classic mistake.

- **Forwarding** is what a router does to *each datagram*: look up the destination in a table and send it out of the right link. It has to take nanoseconds, so it's done in dedicated hardware. This is the **data plane**.
- **Routing** is how that table *gets built*: routers exchange information with their neighbors, work out where each block of addresses can be reached, and fill in the table. It runs continuously in the background, on a normal processor, and reacts to changes in seconds. This is the **control plane**.

Kurose's driving analogy: forwarding is getting through one motorway junction (take this exit); routing is planning the whole trip before you leave. A router does the second in the background so that it only has to do the first when a datagram arrives.

Your Kurose notes put forwarding exactly right: "when a packet arrives at a router, the router examines the address and searches its forwarding table, which maps destination addresses to that router's outbound links, to find the appropriate outbound link."

## The forwarding table and longest prefix match

A forwarding table doesn't list individual addresses. It lists **prefixes**, blocks of addresses that start the same way. That's what makes hierarchical addressing pay off (note 8): one entry covers a whole ISP.

A small example table:

| Prefix | Send out of |
|---|---|
| `103.121.0.0/16` | link 2 |
| `103.121.37.0/24` | link 3 |
| `0.0.0.0/0` (everything else) | link 1 |

When a destination matches more than one entry, the **longest (most specific) prefix wins**:

- `103.121.37.9` matches all three; the `/24` is longest → **link 3**.
- `103.121.200.1` matches the `/16` and the default → **link 2**.
- `8.8.8.8` matches only the default → **link 1**.

The same rule runs in your laptop's own small routing table (note 6).

## What a router does to one datagram

Putting the pieces from earlier notes together, here's one hop:

1. **Receive the frame.** Check the frame check sequence; if bits were corrupted on this link, drop it silently (note 3). Check that it's addressed to this router's MAC address.
2. **Strip the link header.** It has done its job; it belonged only to the previous link.
3. **Check the IP header.** Verify the header checksum. **Decrease the TTL by one.** If it reaches zero, drop the datagram and send an ICMP "time exceeded" message back to the source. Recompute the header checksum, because the TTL changed.
4. **Look up the destination** in the forwarding table (longest prefix match).
5. **Queue** at the chosen output link. Here queueing delay builds up, and if the buffer is full, the datagram is dropped (note 2).
6. **Build a new frame** for the outgoing link: new source MAC (this router's outgoing interface), new destination MAC (the next router's), new check sequence.
7. **Transmit** the whole frame (store-and-forward, note 2).

The source and destination IP addresses don't change at any router (NAT at the edge being the exception).

### Why the TTL exists

TTL stands for *time to live*, but it really counts hops. While routes are changing, routers can briefly disagree, and a datagram can end up going round in a loop. Without a limit, it would circle forever, taking up capacity. The sender sets a starting value (64 on Linux and macOS, 128 on Windows), every router subtracts one, and at zero the datagram is thrown away.

**ICMP** (*Internet Control Message Protocol*) is the network layer's messaging companion: routers and hosts use it to report problems (time exceeded, destination unreachable) and for diagnostics. `ping` uses ICMP echo request and echo reply.

## How the tables get built

### Inside one network

An ISP's own routers talk to each other using an internal routing protocol such as **OSPF** or **IS-IS**. Each router shares what its links look like, every router builds the same map of the ISP's network, and each one computes the shortest paths across it. Inside one organisation, shortest really does mean best.

### Between networks: BGP

The Internet is made of roughly 75,000+ **autonomous systems** (ASes): networks run by one organisation, each with an AS number. Your ISP is one; Google is one; a large university may be one. They connect to each other, and they exchange reachability with **BGP** (*Border Gateway Protocol*):

- Each AS **announces** the address prefixes it can deliver to: "send `103.121.0.0/16` to me."
- Neighbours pass announcements on, adding their own AS number to the **AS path**, so everyone can see the chain of networks a route goes through.
- Each AS **chooses** which of several announcements to use for each prefix, based on its own **policy**.

That last point corrects something from the Gemini chat, which said BGP finds "the shortest and healthiest path." It doesn't. BGP choices are driven mostly by business relationships: an AS prefers routes through its *customers* (who pay it), then through *peers* (free exchange), and only then through its *providers* (whom it pays). Paths on the Internet are often longer than the shortest possible, and the way there and the way back can be completely different.

The full global table now holds around a million IPv4 prefixes. Large routers carry all of it.

> [!question] You asked (Gemini)
> "Is it possible to know the routers that my router "knows"?"

Your home router knows very little: your home network, and "everything else goes to the ISP" (a default route). It doesn't run BGP. Its WAN status page shows its ISP-side address and the ISP gateway it hands everything to. To see the path beyond, use traceroute. To see which network owns an address, look it up on a public BGP tool such as `bgp.he.net`: search for `103.121.37.9` (from your traceroute below) and it will show the AS and prefix it belongs to.

## Traceroute: seeing the path with TTL

Traceroute turns the TTL rule into a measurement tool:

1. Send a probe with TTL = 1. The first router decrements it to 0, drops it, and sends back "time exceeded," revealing its address and the round-trip time.
2. Send a probe with TTL = 2. The second router answers.
3. Keep going until the destination itself replies.

Each hop is probed three times, which is why you see three times per line. Windows `tracert` sends ICMP echo requests as probes; Linux `traceroute` sends UDP by default.

### Your own traceroute, explained

```
C:\Users\ASUS>tracert niruddeshjatra.space
Tracing route to niruddeshjatra.space [216.198.79.1]
  1     1 ms     1 ms     1 ms  192.168.1.1
  2     5 ms     2 ms     4 ms  10.8.66.1
  3     4 ms     2 ms     2 ms  103.121.37.9
  4     4 ms     2 ms     1 ms  10.20.241.1
  5     4 ms     1 ms     1 ms  10.9.6.81
  6     8 ms     5 ms     5 ms  10.198.9.73
  7    30 ms    13 ms    15 ms  10.198.9.53
  8–13  *        *        *     Request timed out.
 14    13 ms    12 ms    11 ms  216.198.79.1
```

- **Hop 1, `192.168.1.1`:** your home router. About 1 ms: one Wi-Fi hop.
- **Hop 2, `10.8.66.1`:** your ISP's first router, with a *private* address. That strongly suggests your home router's own ISP-side address is private too, meaning your ISP uses carrier-grade NAT (note 8 shows how to confirm).
- **Hop 3, `103.121.37.9`:** a public address from your ISP's own block.
- **Hops 4–7, `10.x`:** routers inside the ISP's (or an upstream provider's) network, using private addresses internally. Many carriers do this.
- **Hop 7 shows 30 ms, but the destination shows 11–13 ms.** Times aren't always increasing, and that doesn't mean your traffic was slow at hop 7. The number measures how long that router took to *create* its "time exceeded" reply. Routers forward traffic in fast hardware but generate these replies on a slow, low-priority processor. A slow reply is the router being lazy about traceroute, not delaying your data.
- **Hops 8–13, `* * *`:** those routers didn't reply. Many routers are set not to send "time exceeded" messages, or rate-limit them. Your traffic still went through them; the destination answered at hop 14.
- **Hop 14, `216.198.79.1`, about 12 ms:** the destination. Now use note 2's propagation numbers: 12 ms there and back, even with zero other delay, means the machine that answered is at most about 1,200 km of fibre away. So it is not a server in America (the distance alone would cost well over 100 ms there and back). It's a nearby copy. If niruddeshjatra is hosted on Vercel, `216.198.79.1` is the address Vercel gives for site roots, and it is **anycast**: many Vercel locations around the world announce the same address through BGP, and the network delivers you to the nearest one. You just measured a content delivery network working.

## Try it (Windows)

```
tracert bhara.com
pathping bhara.com
```

`pathping` is Windows' version of `mtr`: it traces the route, then measures loss at each hop for a couple of minutes. Read loss carefully: loss shown at a middle hop that does *not* continue at the later hops is that router ignoring probes, not real loss. Only loss that continues all the way to the final hop affects your traffic.

## Slips to avoid

- **Routing ≠ forwarding.** Routing builds the table in the background; forwarding uses it for each datagram.
- **"BGP picks the shortest path."** It picks by policy, mostly business relationships.
- **"A `*` in traceroute means packets were lost there."** It means that router didn't answer probes.
- **"The reply comes back the same way."** Each direction is routed independently.
- **"TTL is a time."** It's a hop count.

## Check yourself

1. In one sentence each: what is forwarding, what is routing, and where does each run?
2. A table has `10.0.0.0/8 → A`, `10.20.0.0/16 → B`, `0.0.0.0/0 → C`. Where do `10.20.241.1`, `10.9.6.81` and `1.1.1.1` go?
3. List what a router changes in a datagram and its frame at each hop, and what it leaves alone.
4. How does traceroute discover each router, and why can a middle hop show a higher time than the destination?
5. Your traceroute's last hop answered in 12 ms. What does that tell you about where that machine is?

> [!success]- Answers
> 1. Forwarding: per-datagram lookup and send, in fast hardware (data plane). Routing: building the table by exchanging information with other routers, in background software (control plane).
> 2. `10.20.241.1` → B (longest match `/16`); `10.9.6.81` → A; `1.1.1.1` → C.
> 3. Changes: link header (both MACs, check sequence), TTL (minus one) and the IP header checksum. Unchanged: source and destination IP addresses (except at NAT), the transport segment and everything inside.
> 4. Probes with TTL 1, 2, 3…; the router where TTL reaches zero replies "time exceeded." The time measures reply generation on a slow, low-priority CPU, not forwarding.
> 5. At most about 1,200 km of fibre away, since 12 ms round trip at 2×10⁸ m/s is ~1,200 km each way with no other delay: a nearby edge (likely anycast), not a distant origin.

---
← [[m02-08-public-private-addresses-and-nat]] · [[m02-internet|Module 2]] · Next: [[m02-10-dns]] →
