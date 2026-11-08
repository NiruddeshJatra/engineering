---
type: source
module: 2
kind: book
written_by: nasif
---
# Kurose & Ross, *Computer Networking: A Top-Down Approach*, §1.1–1.5

- Read: before 2026-10 (Module 2, Unit 1)
- Also watched: Crash Course CS Ep 28–29
- Reading-order notes built from this chapter: [[m02-01-the-internet-as-a-system]] → [[m02-03-layers-and-encapsulation]]

## My notes

<!-- Nasif's notes, verbatim from raw/ (2026-11). Not edited. Corrections are listed separately below. -->

Internet: network of networks connecting billions of computing devices

Host/End System: The devices that are interconnected such as PC, Mobile, Data center

Client: makes request

Server: serves request

Communication Link: The medium that connects end systems.

Packet Switch: takes packets arriving at on one of its incoming communication links and forwards that packet on one of its outgoing communication Links.

Packet: Before sending data, the sending end system segments the data, adds headen bytes to each segment. The resulting package of information is called packets. Destination end system re-assambles those packets into the original data.

Route: the sequence of communication links and packet switches traversed by a parket from the sending host to the receiving host.

ISP: Internet Service Provider provides a network of communication links and packet switches the end systems can connect to, IsPs are also interconnected.

Internet Socket Interface: a set of rules that the sending program must follow so that the Internet can deliver the deta to the destination program

Protocol: defines the format and the order of messages exchanged between two or mone communicating entities, as well as the actions taken on the transmission and receipt of a message.

Store & forward Transmission Delay: The parket switch must receive the entire packet before transmitting it.

Queueing Delay: If the communication link is busy with the transmission of another packet, the arriving packet must wait in the output buffer.

Packet loss: If the output buffer is full of other packets waiting for transmission, parket loss will occur - either the arriving packet on already queued packet will be dropped.

Forwarding Table: when a packet arrives at a router, the router examines the address and searches ts forwarding table, which maps destination addresses to that router’s outbound links, to find the appropriate outbound link.

Circuit Switching: The resources needed along a path to provide for communication between the end systems ane reserved for the duration of the communication session.

Packet Switching: not reserved.

4 types of delays:

1. Nodal Processing Delay: delay for examining packet's header and determining where to direct it
2. Queueing Delay
3. Transmission Delay
4. Propogation Delay

Throughput: Download speed, streaming rate. How much data is moved at once.

Latency: The delay before the data arrives.

HTTP, SMTP, FTP, TCP, UDP, IP,

Message: Application layer packet

Segment. Transport layer packet

Datagram: Network layer packet

Frame: Link layer packet

OSI model

1. Application Layer
2. Presentation
3. Session
4. Transport
5. Network
6. Link
7. Physical

---

## Corrections (added by Claude, 2026-11-08)

Your notes are kept exactly as written above. These are the points to fix in your head, not on the page.

1. **"Store & forward Transmission Delay: The packet switch must receive the entire packet before transmitting it."** This defines the store-and-forward *mechanism*. Transmission delay is *L/R*: packet size ÷ link rate, the time to push all the bits onto the link. See [[m02-02-packet-switching-and-delay]].
2. **"Throughput: … How much data is moved at once."** Throughput is a *rate* (bits per second actually delivered), not an amount "at once."
3. **Transmission and propagation delay** are listed by name only. Transmission = *L/R* (depends on size and link rate); propagation = *d/s* (depends on distance).
4. **"Packet Switching: not reserved."** Add what it gains: statistical multiplexing, at the cost of queueing delay and loss.
5. **The five-layer Internet model** is missing; only OSI's seven layers are listed. The Internet uses five: application, transport, network, link, physical. See [[m02-03-layers-and-encapsulation]].

Correct and worth keeping as written: the protocol definition (format, order, actions), the forwarding-table definition, and the unit names (message, segment, datagram, frame).
