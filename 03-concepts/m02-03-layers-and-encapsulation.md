---
type: concept
module: 2
unit: 1
order: 3
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 3. Layers and encapsulation

*Where we are: we know what the Internet is made of and how packets share links. Before sending anything, we need the idea that organizes everything else: dividing the work into layers.*

## The problem

To get one web page from a server to your screen, an enormous number of different jobs must be done:

- Say what you want ("give me the home page").
- Deliver the data to the right program on the right machine.
- Make sure nothing is lost, duplicated or out of order.
- Find a path across dozens of independently run networks.
- Get the data from one device to the next across each individual cable or radio link.
- Turn bits into voltages, light pulses or radio waves.

These jobs are done by very different things: your browser, your operating system, your Wi-Fi chip, routers in your ISP, a fibre transceiver under the sea. They are made by different companies and change at different speeds. Wi-Fi gets a new version every few years; HTTP changes rarely; TCP has barely changed in decades.

If one giant program did all of it, every browser would need to know about every kind of cable, and every new radio technology would require rewriting every application. Nothing could change without everything changing.

> [!question] You asked me (Claude, Oct 9)
> "But my question is, why do they exist and why is so many layers needed? This is very confusing. I mean, why the direct message that I want to send through a network wants to add a header first, then some of the headers in the second layer, then some more in the third layer. Why so many?"

> [!question] And later, to Gemini
> "Do you know I have been trying to understand all of this for a long long time and I'm still not satisfied. Every time I ask this type of question anywhere, I hit a wall, like layer 2, layer 3. It's like, just for maintaining the layers or the rules, we are sacrificing convenience or simplicity."

That frustration is reasonable, and the answer below doesn't pretend layering is free. It has costs. The question is what it buys.

## An analogy that fits: flying

Kurose explains layering with air travel, and it maps well. Think about what happens when you fly from Chattogram to Kuala Lumpur:

| Departure | | Arrival |
|---|---|---|
| Buy ticket | ↔ | Complain about ticket |
| Check baggage | ↔ | Collect baggage |
| Board at gate | ↔ | Leave at gate |
| Take off from runway | ↔ | Land on runway |
| Follow the flight route | → → → | Follow the flight route |

Each row is a separate job done by separate people. The baggage staff never touch the runway. The pilot doesn't check tickets. Each row **uses the service of the row below** and **provides a service to the row above**: baggage handling assumes you will get from gate to gate somehow, and doesn't care whether the plane is an Airbus or a Boeing. And each row on the departure side has a **counterpart** on the arrival side that undoes or completes its work.

If the airline replaces its planes, the ticket office doesn't change. That independence is the whole point.

## The five layers of the Internet

The Internet divides the work into five layers. Each layer has a job, a name for its unit of data, and a place where it runs.

| Layer          | Its one job                                             | Unit of data | Runs in                                     | Example protocols    |
| -------------- | ------------------------------------------------------- | ------------ | ------------------------------------------- | -------------------- |
| 5. Application | Say what is being asked for and what the answer means   | **message**  | Programs (browser, web server)              | HTTP, DNS, DHCP      |
| 4. Transport   | Deliver data to the right *program*, reliably if needed | **segment**  | Operating system kernel, hosts only         | TCP, UDP             |
| 3. Network     | Deliver data to the right *machine*, across networks    | **datagram** | Kernel on hosts; dedicated chips in routers | IP                   |
| 2. Link        | Deliver data to the *next device* on one link           | **frame**    | Network card (NIC) and its driver; switches | Ethernet, Wi-Fi      |
| 1. Physical    | Move individual bits as signals                         | bits         | Transceiver hardware                        | Copper, fibre, radio |

"Packet" is the general word for any of these units. When precision matters, use the layer's own name. In particular, the network-layer unit is a **datagram**, and a **segment** lives *inside* a datagram. Your Kurose notes already had this right: "Message: application layer packet; Segment: transport layer packet; Datagram: network layer packet; Frame: link layer packet."

## Encapsulation: why each layer adds a header

Here is the key idea for your "why so many headers" question.

**A header is a note from one layer to its counterpart on the other side.** The transport layer on your laptop can't phone the transport layer on the server. The only thing that travels is the data itself. So anything one layer needs to tell its counterpart has to be written onto the data before it goes down, and read off when it comes up.

Going down on the sender:

```
Application:  [ HTTP message ]
Transport:    [ TCP header | HTTP message ]                        ← segment
Network:      [ IP header | TCP header | HTTP message ]            ← datagram
Link:         [ Eth header | IP header | TCP header | HTTP | FCS ] ← frame
```

Going up on the receiver, each layer reads *its own* header, acts on it, removes it, and passes the rest upward. Each layer treats everything inside as an opaque payload it does not need to understand. The network layer doesn't know or care that there is an HTTP request inside.

Each header answers a different question for a different reader:

| Header | Question it answers | Who reads it |
|---|---|---|
| HTTP (application) | What is being asked for? | Only the server program |
| TCP (transport) | Which program, and which bytes are these? | Only the two end machines |
| IP (network) | Which machine, anywhere on the Internet? | Every router on the path |
| Ethernet/Wi-Fi (link) | Which device is next, on this one link? | Only the two ends of that link |

### Why the headers can't be merged into one

1. **Different readers.** Routers must read the IP header for every datagram at enormous speed. If the router had to parse a merged header containing HTTP fields, it would be slower, and it would have to understand HTTP.
2. **Different lifetimes.** The link header is thrown away and rebuilt at every router, because each link may be a different technology and the "next device" changes. The IP header travels the whole way. The TCP header is untouched by everything in the middle. One merged header would have to be partly rewritten, partly kept, at every hop.
3. **Independent change.** HTTP/2 replaced HTTP/1.1's format without any router noticing. IPv6 replaced IPv4 underneath without HTTP changing. That's only possible because the headers are separate.

## End-to-end and hop-by-hop

Layering produces two kinds of conversation happening at the same time:

- **End-to-end:** application, transport and network addresses describe the *two ends*: your browser and the web server, your OS and the server's OS, your machine and the server's machine. These stay the same for the whole trip (with one exception, NAT, in note 8).
- **Hop-by-hop:** the link layer describes only *the current link*. Its header says "from this device to the next device," and it is replaced at every hop.

That is why the devices in the middle don't need all five layers:

| Layer | Your laptop | Link-layer switch | Router | Server |
|---|---|---|---|---|
| Application | ✓ | | | ✓ |
| Transport | ✓ | | | ✓ |
| Network | ✓ | | ✓ | ✓ |
| Link | ✓ | ✓ | ✓ | ✓ |
| Physical | ✓ | ✓ | ✓ | ✓ |

Routers stop at layer 3, switches at layer 2. Keeping the middle of the network simple, and putting the intelligence at the ends, is a deliberate principle (the *end-to-end principle*). It's why the middle can be fast and cheap, and why new applications can be invented without anyone upgrading the routers.

## The OSI model

You'll see the **OSI model** with seven layers: application, presentation, session, transport, network, data link, physical. It was designed in the 1980s as an international standard and is still used as vocabulary ("a layer-7 load balancer", "a layer-2 switch").

The Internet does not have separate presentation (data format, encryption, compression) and session (managing a dialogue) layers. Those jobs are left to applications. TLS encryption, for example, is done by a library inside the application. So: learn the five-layer model for how the Internet works; know the seven OSI names so you understand the vocabulary.

## The costs of layering

Your frustration was partly right. Layering has real costs.

- **Overhead.** Ethernet, IPv4 and TCP add at least 58 bytes to every frame: 14 + 20 + 20 bytes of headers, plus a 4-byte check at the end. On a full-sized frame that's about 4%. On a one-byte keystroke, it's enormous.
- **Repeated work.** Several layers check for errors (see below). Some work is done twice.
- **Leaks.** Real systems break the rules when it pays. Your home router reads and rewrites TCP port numbers (layer 4) even though it's a layer-3 device (note 8). TCP's checksum covers the IP addresses, which belong to the layer below.
- **Translation steps.** Two address systems (IP and MAC) mean something must translate between them (ARP, note 7).

So "we sacrifice some convenience for the layers" is true. What we get in return is that thousands of independent organizations can build and replace parts of the system without coordinating with each other. For a system nobody owns, that is the only way it could have grown.

> [!tip] When the answer to "why" is history
> Some of your "why" questions have the answer "because of the order in which things were built, and replacing them now would cost more than it saves." That is a real engineering answer, not a dodge. Note 7 (MAC addresses) is the clearest example. Accept those answers when the history is stated plainly; push back when someone dresses history up as a law of nature.

## Who checks what, and why there are so many checks

Errors are checked at several layers, and each check catches something the others can't.

| Who checks | What | What it catches |
|---|---|---|
| Receiving network card, at every hop | Frame check sequence (a CRC), destination MAC | Bits corrupted on *that one link*; frames meant for someone else |
| Each router (IP) | IPv4 header checksum, TTL | A corrupted header that would misroute the datagram; routing loops |
| Receiving host (IP) | Destination address, protocol field | Misdelivery; which transport protocol gets the payload |
| Receiving TCP | Checksum over the whole segment, sequence numbers | Corruption *anywhere* on the path (including inside a faulty router's memory), loss, duplicates, reordering |
| TLS (if used) | Cryptographic integrity tag | Deliberate tampering |
| The application | Valid request, permissions | Malformed or unauthorised requests |

The link check guards one hop only. Once a router has accepted a frame and rebuilt a new one, that check is gone. A router could corrupt data in memory after the check passed, and a lost datagram is invisible to every link. Only the two endpoints can confirm that the *whole* trip worked. Lower-layer checks are an optimization; the end-to-end check is the guarantee.

## Slips to avoid

- **"Packet" for everything** when precision is asked for. Segment (transport) sits inside a datagram (network), which sits inside a frame (link).
- **"A layer is a piece of hardware."** A layer is a set of responsibilities. The network layer is software in your kernel and silicon in a router.
- **"Routers read HTTP."** Routers stop at layer 3. They don't know there's a web page inside.

## Check yourself

1. Name the five layers, each one's job in a few words, and its unit of data.
2. Why can't the link header and the IP header be merged into one?
3. Which layers does a router implement, and why does it stop there?
4. Give one real cost of layering and one thing it makes possible.
5. The frame check sequence already catches corrupted bits. Why does TCP check again?

> [!success]- Answers
> 1. Application: what's asked, message. Transport: right program, reliably if needed, segment. Network: right machine across networks, datagram. Link: next device on one link, frame. Physical: bits as signals.
> 2. Different readers (every router reads IP; only the two ends of a link read the link header), different lifetimes (link header rebuilt each hop; IP header travels end to end), independent evolution.
> 3. Physical, link and network. Its job is only to move datagrams toward their destination; keeping the middle simple makes it fast and lets applications change without upgrading routers.
> 4. Cost: header overhead, repeated checks, leaks like NAT, translation steps like ARP. Gain: independent change (IPv6 under unchanged HTTP; HTTP/2 over unchanged routers; new Wi-Fi without new browsers).
> 5. The link check covers only one hop. Corruption inside a router, or loss, is invisible to it. Only the endpoints can verify the whole path.

---
← [[m02-02-packet-switching-and-delay]] · [[m02-internet|Module 2]] · Next: [[m02-04-ports-sockets-and-udp]] →
