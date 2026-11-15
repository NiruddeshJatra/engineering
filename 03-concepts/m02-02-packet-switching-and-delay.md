---
type: concept
module: 2
unit: 1
order: 2
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 2. Packet switching, delay, loss and throughput

*Where we are: we know the Internet is hosts, links and packet switches. Now: how do millions of conversations share the same cables, and what does that sharing cost?*

## The problem: sharing the wires

There are far more conversations than cables. The fibre between Chattogram and Dhaka carries traffic for hundreds of thousands of people at once. Somehow, all of them have to take turns on the same links.

There are two basic ways to share.

## Circuit switching: reserve a lane

The old telephone network reserved resources. When you dialed a number, the network set up a **circuit**: a fixed slice of capacity on every link from you to the other phone, held for you alone until you hung up. On a link, these dedicated slices can be created in two ways:

- **Frequency-Division Multiplexing (FDM):** Everyone speaks simultaneously, but each call is assigned its own unique frequency band (or pitch range) using a modulated carrier wave, ensuring they never bleed into each other—just like different radio stations broadcasting at the same time on separate frequencies.

- **Time-Division Multiplexing (TDM):** Everyone shares the entire frequency bandwidth of the wire, but they take turns in rapid succession. The network chops time into repeating slots, giving each ongoing call 100% of the channel's capacity for a brief fraction of a millisecond at a time, cycling through calls thousands of times per second to create a seamless, full-duplex illusion.

The advantage is a guaranteed, constant rate. Nobody else can slow you down, because your slice is yours.

The cost is waste. During a phone call there are pauses; during a web session there are long pauses while you read a page. A reserved slice sits idle during every pause, and nobody else may use it.

## Packet switching: take turns on demand

The Internet does not reserve anything. A host cuts its data into packets and sends each one when it is ready. Every link carries packets from whoever has something to send at that moment. Capacity goes to whoever needs it right now. This is called **statistical multiplexing**.

Why is that better for computer traffic? Because computer traffic is *bursty*: short bursts of activity separated by long silences. Kurose gives a clean example:

- A link of 1 Mbps. Each user, when active, sends at 100 kbps, but is active only 10% of the time.
- **Circuit switching** must reserve 100 kbps per user, so it can support exactly 10 users.
- **Packet switching** can support 35 users. With 35 users, the chance that more than 10 are active at the same moment is about 0.0004. For almost all of the time, everyone gets full speed, and the link carries 3.5 times as many people.

The price of packet switching is that when too many people *do* send at once, packets have to wait, and sometimes they are lost. That is what the rest of this note is about.

| | Circuit switching | Packet switching |
|---|---|---|
| Resources | Reserved for the whole session | Used on demand, not reserved |
| Guarantee | Fixed rate, no queueing | None: delay varies, loss possible |
| Efficiency with bursty traffic | Poor (idle reservations) | High (statistical multiplexing) |
| Example | Old telephone network | The Internet |

## Store-and-forward: a mechanism, not a delay

Most packet switches use **store-and-forward transmission**: a router must receive the *entire* packet before it can start sending the first bit of it onto the next link. It stores the packet in memory, checks it, decides where it goes, then forwards it.

A consequence: one packet of *L* bits crossing *N* links, each with rate *R*, takes at least *N × L/R* seconds just to be pushed onto each link in turn, because no link can start before the previous one has finished delivering the whole packet.

Keep this separate in your head from the delays below. Store-and-forward is *how the switch behaves*. The delays are *how long each step takes*.

## The four delays at each hop

When a packet arrives at a router and leaves on the next link, four delays add up. Kurose calls their sum the **nodal delay**.

**1. Processing delay** (*d*<sub>proc</sub>). The time to examine the packet's header, check it for bit errors, and decide which output link it goes to. In modern routers this is microseconds or less.

**2. Queueing delay** (*d*<sub>queue</sub>). The time the packet spends waiting in the output buffer because the outgoing link is busy sending other packets. It can be zero (empty queue) or very large (congested link). It is the only one of the four that is genuinely unpredictable, because it depends on everyone else's traffic.

**3. Transmission delay** (*d*<sub>trans</sub>). The time to push *all* the packet's bits onto the link:

$$d_{trans} = \frac{L}{R} \quad \text{(packet length in bits ÷ link rate in bits per second)}$$

It depends on the **packet's size** and the **link's rate**. It has nothing to do with distance.

**4. Propagation delay** (*d*<sub>prop</sub>). Once a bit is on the link, the time it takes to travel to the other end:

$$d_{prop} = \frac{d}{s} \quad \text{(distance ÷ propagation speed)}$$

In copper and fibre, signals travel at roughly 2 × 10<sup>8</sup> m/s, about two-thirds of the speed of light in a vacuum. It depends on **distance**. It has nothing to do with packet size or link rate.

### The caravan analogy (Kurose's version)

Transmission and propagation delay are the pair everyone mixes up, so here is the analogy Kurose uses.

A caravan of 10 cars drives along a highway with toll booths every 100 km. Each toll booth takes 12 seconds to let one car through. The cars drive at 100 km/h. The caravan stays together: no car leaves a booth until the whole caravan has passed through it.

- The **cars** are bits; the **caravan** is a packet.
- **Transmission delay** is the time for the booth to push the whole caravan through: 10 cars × 12 s = **120 seconds**. A longer caravan (bigger packet) or a slower booth (slower link) makes it longer.
- **Propagation delay** is the time for a car to drive to the next booth: 100 km at 100 km/h = **1 hour**. A longer highway makes it longer. The caravan's length doesn't matter.
- **The caravan waiting together at the booth** is store-and-forward.

### Real numbers

| Situation | Calculation | Result |
|---|---|---|
| 1,500-byte packet on a 10 Mbps link | 12,000 bits ÷ 10,000,000 bps | 1.2 ms transmission |
| Same packet on a 1 Gbps link | 12,000 ÷ 1,000,000,000 | 12 µs transmission |
| 1,000 km of fibre | 1,000,000 m ÷ 2×10<sup>8</sup> m/s | 5 ms propagation |
| Chattogram to Singapore and back (~2,600 km each way, straight line) | 2 × 13 ms | at least ~26 ms round trip, before anything else |

A faster internet plan shrinks transmission delay. It cannot shrink propagation delay. That is why content delivery networks move servers physically closer to users instead of only buying faster links.

### Queueing in more detail

Let *a* be the average rate at which packets arrive at a queue (packets per second), each of *L* bits, on a link of rate *R*. The ratio *La/R* is the **traffic intensity**.

- If *La/R* is small, packets rarely find the queue occupied: queueing delay is near zero.
- As *La/R* approaches 1, queueing delay grows very quickly. The link is almost always busy, so almost every packet waits.
- If *La/R* is greater than 1, packets arrive faster than they can leave. The queue grows without limit.

Real buffers are finite, which leads to the next problem.

## Packet loss

A router's output buffer has a fixed size. When a packet arrives and the buffer is already full, there is nowhere to put it, so the router **drops** it: either the arriving packet or one already waiting. The packet simply disappears. Nobody is notified at that moment.

Loss is not a malfunction. It is the normal pressure valve of a packet-switched network. Note 11 shows how TCP notices the gap and uses loss as a signal to slow down.

## End-to-end delay

For a path of *N* links with no congestion (no queueing), the delay for one packet is roughly:

$$d_{end-end} = N \times (d_{proc} + d_{trans} + d_{prop})$$

With congestion, each hop also adds its own queueing delay. Every hop has a transmitter, a buffer and a link, so the four delays repeat at every hop. Different hops are dominated by different delays.

| Where | Usually dominant | Why |
|---|---|---|
| Long-distance fibre | Propagation | Distance can't be bought away |
| Congested home uplink | Queueing | Many packets waiting for a slow link |
| Slow link (old Wi-Fi, weak mobile signal) | Transmission | Bits go onto the link slowly |
| Overloaded router or host | Processing | Not enough CPU or forwarding capacity |

## Throughput, bandwidth and latency: three different things

These three words are used loosely in everyday talk. Keep them apart.

- **Bandwidth** (or transmission rate) is a link's *capacity*: how fast it can push bits, in bits per second. It is a property of one link.
- **Throughput** is the *rate* at which data actually arrives at the receiver, end to end, in bits per second. A "download speed" test measures throughput.
- **Latency** is *time*: how long data takes to get from one end to the other (or there and back, which is the round-trip time).

Throughput is limited by the slowest link on the path, the **bottleneck**. If a server's link runs at 1 Gbps but your home link runs at 20 Mbps, your throughput can never exceed 20 Mbps, however fast the server is. If the bottleneck link is shared with other users, your share is smaller still.

High bandwidth does not mean low latency. A satellite link can carry a lot of data per second (high throughput) but each bit takes a long time to travel up and down (high latency).

## Slips to avoid

These two are from your own Kurose notes. They are the kind of precision slip that loses marks.

- **"Store & forward transmission delay: the packet switch must receive the entire packet before transmitting it."** That sentence describes the store-and-forward *mechanism*. Transmission delay is *L/R*, the time to push the bits onto the link. They are related (store-and-forward is why transmission delay is paid again on every link), but they are not the same thing.
- **"Throughput: how much data is moved at once."** Throughput is a *rate*, data per unit of time (bits per second). "At once" makes it sound like a quantity.

Two more classic traps:

- Transmission delay depends on **size and link rate**, never distance. Propagation delay depends on **distance**, never size.
- "A faster plan fixes lag." It fixes transmission delay, sometimes queueing. It never fixes propagation.

## Check yourself

1. In one sentence each: what does circuit switching reserve, and what does packet switching gain by not reserving?
2. Which of the four delays depends on packet size? Which on distance? Which is unpredictable?
3. A 1,500-byte packet crosses 3 links, each 10 Mbps and 1,000 km of fibre. Ignoring processing and queueing, what is the total delay? Which part dominates? What changes if every link becomes 1 Gbps?
4. What happens when a packet arrives at a full output buffer, and who is told?
5. Your laptop's Wi-Fi link is 300 Mbps, your ISP connection is 20 Mbps, the server's link is 10 Gbps. What is your best possible throughput, and why?

> [!success]- Answers
> 1. Circuit switching reserves a fixed slice of capacity on every link for the whole session. Packet switching gains statistical multiplexing: idle capacity goes to whoever needs it, so many more bursty users share the same link.
> 2. Size: transmission (*L/R*). Distance: propagation (*d/s*). Unpredictable: queueing.
> 3. Transmission 1.2 ms × 3 = 3.6 ms; propagation 5 ms × 3 = 15 ms; total ≈ 18.6 ms. Propagation dominates. At 1 Gbps, transmission drops to 0.036 ms in total, so total ≈ 15.04 ms: almost nothing changes, because distance was the cost.
> 4. It is dropped (the arriving packet or a queued one). Nobody is told at that moment; the sender's TCP infers the loss later (note 11).
> 5. 20 Mbps: throughput is capped by the bottleneck, the slowest link on the path.

---
← [[m02-01-the-internet-as-a-system]] · [[m02-internet|Module 2]] · Next: [[m02-03-layers-and-encapsulation]] →
