
Q: I asked my teacher this:
Before I do the things you told me to, explain or describe the process of sending and receiving data in details, with each layer's involvement, which part of the software or hardware or part of network equipment handles which layer, what information gets added, who checks what, how the four types of delay involved here, which delay can happen in which equipment, which state, why this header is needed, who adds that, who extracts that - everything. Even the questions I haven't asked. I don't want to know the 'what' only, I need to know the 'why' behind each of them one by one too. Also, describe the protocols needed in this exchange of information and why they are needed. You can make this as long as it needs so that I have barely no question left to ask. Yes, for my level and my goal, I don't need to know the intricate details at each step, but I need to know the process overall and the reasons behind each of them to build intuition and have a basic understanding overall. I can't blindly follow even if they serve the purpose.

Then he replied but the explanation seemed a bit off, without any flow, confusing to understand, described in a way as if I know everything beforehand. Can you do it better or answer my prompt in a different way?

# The full journey of one web request, with the why

**The scenario:** you open `bhara.com`. The request goes from your laptop over Wi-Fi to your home router, then to your ISP, through several routers, and finally to the server that hosts the site. I'll follow one request out and one response back.

## 1. The one idea that holds everything together

There are two different journeys happening at once, and most confusion comes from mixing them up.

- **End-to-end:** the browser talks to the web server (HTTP), your OS talks to the server's OS (TCP), and the IP addresses say "laptop ↔ server". These stay the same for the whole trip.
- **Hop-by-hop:** the link layer only cares about the *next device on this one wire or radio link*. Its header is thrown away and rebuilt at every router.

Here is which devices implement which layer:

| Layer | Laptop | Home router | ISP routers | Server |
|---|---|---|---|---|
| Application | ✓ | | | ✓ |
| Transport | ✓ | | | ✓ |
| Network | ✓ | ✓ | ✓ | ✓ |
| Link | ✓ | ✓ | ✓ | ✓ |
| Physical | ✓ | ✓ | ✓ | ✓ |

**Why routers stop at layer 3:** a router's only job is to move datagrams toward their destination, as fast as possible. If routers also parsed TCP and HTTP, they would be slower and more complicated. Every change to TCP would also require upgrading the whole Internet's middle. Keeping the smarts at the two ends is a deliberate design choice (the end-to-end principle, which comes up again in section 10).

One exception: your home router also rewrites TCP port numbers (NAT, section 6). It breaks the clean layering, and I'll explain why it's tolerated.

## 2. The cast: what actually runs each layer

| Layer | What runs it | Software or hardware |
|---|---|---|
| Application | Browser; the web server program | Software, in user space |
| Transport | TCP inside the OS kernel | Software, **hosts only** |
| Network | IP in the OS kernel (hosts); dedicated forwarding chips in routers | Software on hosts, hardware in routers |
| Link | NIC driver in the kernel, plus the NIC chip; Wi-Fi access point; switches | Both |
| Physical | The transceiver: radio, laser, or voltage drivers on cable | Hardware |

**Why routers use dedicated chips:** a core router handles billions of packets per second. A general CPU running software cannot make a forwarding decision for each one in that time, so the fast path is built into hardware. A normal CPU does the slow work in the background (routing protocols, section 6).

**Why the physical layer is separate:** it turns bits into signals (radio waves, light pulses, voltages). If the layers above had to care, every app would need to know whether you're on Wi-Fi or fibre.

## 3. Before your request exists: three setup steps

These happen first, and the whole exchange depends on them.

**DHCP (when you joined the Wi-Fi).** Your laptop had no IP address. It broadcasts a request, and the router answers with four things: an IP address, a subnet mask, a default gateway (the router's address), and a DNS server address.

*Why this exists:* MAC addresses are burned into hardware, but IP addresses depend on where you are, because they encode which network you're on. They can't be pre-assigned, so they are assigned automatically. The subnet mask lets your laptop decide whether a destination is on its own network or somewhere else.

**ARP (to reach the gateway).** The link layer needs a MAC address to build a frame, but your laptop only knows the gateway's IP. It broadcasts "who has 192.168.1.1?" on the local link, the gateway replies with its MAC, and the laptop caches the answer.

*Why this exists:* IP and MAC are independent address systems by design, so a translation step is needed. It's a broadcast because the laptop doesn't yet know who to ask. It only works on one local link and never crosses a router.

**DNS (to turn `bhara.com` into an IP address).** The laptop sends a small query to the DNS server. The query itself travels through the full stack: UDP, IP, and a frame to the gateway.

*Why this exists:* names are for humans and stay stable, while addresses change as sites move servers. The name-to-address step lets one name point at different machines over time. *Why UDP:* the request and reply are tiny, so a handshake would double the cost, and if one is lost the app just asks again. Answers are cached (the TTL says for how long). The details are Unit 2.

## 4. The connection: TCP handshake, then TLS

Before any data flows, your OS and the server's OS exchange three tiny segments: SYN, SYN-ACK, ACK.

*Why a handshake first:*
- Both sides need to confirm the other exists and is willing to talk.
- Both pick starting sequence numbers, so bytes can be tracked, ordered, and stale packets can be told apart from current ones.
- Both allocate memory (buffers) for the connection.

*The cost:* one full round trip before the first byte of the request. This is why distance hurts so much, which comes back in section 9.

For HTTPS, a **TLS** handshake follows (one or two more round trips). It agrees on encryption keys and proves the server is really `bhara.com` using its certificate. TLS sits between HTTP and TCP, so the HTTP message is encrypted *before* it reaches TCP. The IP and TCP headers stay readable, because routers need them. Details are Unit 3.

## 5. Sending the request: going down the stack on your laptop

```
Application:  [ HTTP message ]
Transport:    [ TCP hdr | HTTP message ]                          = segment
Network:      [ IP hdr | TCP hdr | HTTP message ]                 = datagram
Link:         [ Eth hdr | IP hdr | TCP hdr | HTTP message | FCS ] = frame
```

**Step 1: Application.** The browser builds `GET / HTTP/1.1` plus headers (`Host: bhara.com`, accepted formats, cookies) and hands it to the OS through a **socket**. The socket is the doorway between your program and the kernel.

*Why the HTTP headers:* `GET /` says what you want. `Host` is needed because one server's IP address often hosts many websites. Cookies carry your login state, since HTTP itself doesn't remember you.

**Step 2: Transport (TCP).** TCP adds a header: source port, destination port, sequence number, acknowledgment number, window size, flags, and a checksum.

- *Why ports:* IP gets data to the right *machine*, but a machine runs a browser with ten tabs, an email client, a game. Ports tell the kernel which program should get the data. Your browser picks a random source port (say 52314). The server listens on 443 (HTTPS) or 80 (HTTP).
- *Why sequence numbers:* IP doesn't guarantee order or delivery, so TCP numbers bytes to put them back in order and detect gaps.
- *Why segmentation:* a link has a maximum frame size (usually 1500 bytes). TCP cuts data into pieces of about 1460 bytes (the MSS). It also means a loss only costs one small piece, not the whole transfer, and different connections can share a link fairly by taking turns. Your request is tiny, so it fits in one segment. The response won't (section 8).
- TCP keeps a copy of each segment until it's acknowledged, so it can resend if needed.

**Step 3: Network (IP).** IP adds a header: source IP, destination IP (from DNS), TTL (e.g. 64), a protocol field (6 means "this carries TCP"), a header checksum, and a length.

- *Why IP addresses:* ports and MACs mean nothing across networks. Only IP addresses identify where to deliver.
- *Why the protocol field:* it tells the receiving host which transport protocol gets the contents, the same "who's this for" problem as ports, one layer down.
- *Why TTL:* routers decrement it at every hop and drop the datagram at zero. Without it, a routing loop would circulate datagrams forever.

Then your laptop makes its first forwarding decision. Using the subnet mask, it checks whether the destination is on its own network. It isn't, so it sends to the **default gateway**.

**Step 4: Link (Wi-Fi/Ethernet).** The NIC driver adds a header with the source MAC (your laptop), the destination MAC (**the gateway, not the server**, learned through ARP), and a type field (IPv4, IPv6, or ARP). It also adds a trailer, the FCS (a CRC checksum).

- *Why the link header exists:* many devices share the same medium (Wi-Fi is one shared radio channel), so every frame must say who it's for.
- *Why the FCS:* radio and cables corrupt bits, and the receiver needs a cheap way to detect that.

**Step 5: Physical.** Bits become radio waves. The NIC first waits for the channel to be free, which is a form of queueing.

## 6. One hop: what a router does

This repeats at every router on the path.

1. **Receive.** The bits become a frame. The router checks the FCS and drops the frame if it's corrupt. Nobody tells the sender. TCP notices later. It also checks whether the destination MAC is its own.
2. **Strip the link header** and hand the datagram to IP.
3. **IP checks.** It verifies the header checksum, decrements TTL, and recomputes the checksum, because TTL changed. If TTL hits 0 it drops the datagram and sends back an ICMP "time exceeded" message. This is exactly how `traceroute` works.
4. **NAT (home router only).** The router rewrites your private source address (192.168.x.x) to the ISP-assigned public address and swaps in a unique source port. It remembers the mapping so it can translate replies back. *Why:* IPv4 has only about 4.3 billion addresses, so homes must share one. *Cost:* the router now touches layer 4 information, and outside machines can't easily start connections to you.
5. **Look up the destination IP** in the forwarding table to pick the output link. The table matches address *prefixes*, not individual addresses, which is possible because IP addresses are hierarchical (an ISP owns a block, and its routers need one entry for the whole block).
6. **Queue.** The datagram waits in the buffer of the chosen output link. If the buffer is full, it's dropped, and that is packet loss.
7. **Rebuild the link header.** The new source MAC is the router's outgoing interface, and the destination MAC is the next router's. A new FCS is computed.
8. **Transmit the whole frame.** This is store-and-forward: the router receives the entire frame before sending any of it on.

**Why the MAC changes at every hop but the IP doesn't:** a MAC means "the next device on this link" and nothing more. Links also use different technologies (Wi-Fi, fibre, Ethernet), each with its own header format, so the frame must be rebuilt for each. The IP address means "the final destination" and has to survive the whole trip.

**The control plane.** Routers build their forwarding tables with routing protocols (OSPF inside one ISP, BGP between ISPs). *Why separate:* forwarding must take nanoseconds per packet, but computing routes takes seconds and changes when links fail. So tables are prepared in the background and consulted per packet. This is the "routing protocol builds the table, the table is used per packet" distinction from earlier.

**Switches (layer 2).** Inside a LAN or data centre, a switch forwards frames by MAC address using a table it learns by watching which port each source MAC arrives on. It never looks at IP. *Why they exist:* they connect many devices on one link cheaply, without routing.

## 7. Arrival: going up the stack on the server

The last router sends a frame whose destination MAC is the server's (found via ARP on that network).

1. **NIC:** checks the FCS and whether the destination MAC is its own. It then interrupts the CPU, and the driver passes the datagram up.
2. **IP:** checks the destination IP is its own and the header checksum is valid. The protocol field says 6, so it hands it to TCP.
3. **TCP:** verifies the checksum, checks the sequence number, buffers the data, and sends back an ACK. It uses the four values (source IP, source port, destination IP, destination port) to find the right socket.
4. **Web server program:** reads bytes from the socket, parses the HTTP request, fetches the page or queries a database, and builds a response.

The server's own processing time is *not* one of the four network delays, but you feel it in the total page load.

## 8. The response, and why segments matter

The response goes through the same process in reverse. A 100 KB page becomes roughly 70 segments, each with its own IP header and each in its own frame.

Segments can arrive out of order or go missing, because queues drop packets and paths can change mid-transfer. The receiver's TCP puts them back in order using sequence numbers, ACKs what it got, and the sender resends whatever goes unacknowledged.

TCP also solves two different problems, and these are easy to mix up:
- **Flow control** protects the *receiver*. The receiver advertises how much buffer space it has, and the sender never sends more than that.
- **Congestion control** protects the *network*. The sender infers congestion from loss and delay, and slows down so routers' queues don't overflow.

Finally, the connection closes (FIN), and the browser starts rendering.

## 9. The four delays: where each one happens

Every hop (a device plus the link leaving it) has all four.

| Delay | Where it happens | Depends on | Typical size |
|---|---|---|---|
| **Processing** | Inside every device that handles the datagram: host kernel, each router | Header work (checksum, lookup) | Microseconds or less |
| **Queueing** | Waiting for an outgoing link: laptop NIC, home router, every ISP router, server | Traffic load versus link rate. Random. | 0 to hundreds of ms |
| **Transmission** | At the sender of each link: laptop radio, each router's output port, server NIC | Packet size ÷ link rate | 12 µs at 1 Gbps; 1.2 ms at 10 Mbps (for 1500 B) |
| **Propagation** | Along each link's medium | Distance ÷ signal speed (~2×10⁸ m/s) | About 5 ms per 1,000 km |

**Because of store-and-forward, transmission delay is paid on every link, not once.** A frame crossing N links of equal speed spends N × (L/R) in transmission delay.

Where each one dominates:
- **Wi-Fi hop:** transmission and queueing (the radio channel is shared). Propagation is negligible.
- **Home uplink to ISP:** usually queueing, because it's the slowest link and the bottleneck.
- **Long-haul fibre:** propagation. A server 10,000 km away costs 50 ms one-way before anything else, and you cannot buy your way past that. That is the reason CDNs exist: they move the server closer.
- **Throughput:** the slowest link on the path sets your achieved rate.

`ping` shows these four, summed over every hop and counted twice (there and back).

## 10. Who checks what, and why so many checks

| Who | What it checks | What that catches |
|---|---|---|
| Receiving NIC (every hop) | FCS, destination MAC | Corruption on that one link; frames meant for others |
| Router (IP) | Header checksum, TTL | A corrupted header (which would misroute); loops |
| Receiving host (IP) | Destination IP, protocol field | Misdelivery; which transport gets it |
| Receiving TCP | Checksum over the segment, sequence numbers, ports | Corruption *anywhere* along the path, loss, reordering, duplicates |
| Application | Valid HTTP, permissions | Malformed or unauthorised requests |

**Why check at several layers:** each layer protects only what it can see. The FCS guards one hop, but a faulty router could corrupt data in memory *after* that check passes. Lost packets are invisible to every link. Only the two endpoints can verify that the whole trip worked. This is the end-to-end principle: put a function where it can be done completely, and treat lower-layer help as an optimization.

## 11. The protocols in this exchange, and why each exists

A **protocol** defines the format and order of messages and the actions taken when they're sent or received. Many vendors (your laptop, a Cisco router, a Linux server) interoperate only because they all follow the same published protocols.

| Protocol | Layer | Job | Why it's needed | Unit |
|---|---|---|---|---|
| HTTP | Application | Request and reply format for web content | Browser and server need a shared language | 3 |
| TLS | Between app and transport | Encryption and server identity | Without it, anyone on the path can read or alter the data | 3 |
| DNS | Application | Name → IP address | Humans use names; machines route by address | 2 |
| TCP | Transport | Reliable, ordered delivery; flow and congestion control | IP alone loses, duplicates, and reorders | 2 |
| UDP | Transport | Bare ports plus a checksum | Cheap delivery when speed beats reliability (DNS) | 2 |
| IP | Network | Addressing and forwarding across networks | A common address space joining unlike networks | 1 |
| ICMP | Network | Error and diagnostic messages | Routers need a way to report problems (ping, traceroute) | 1 |
| ARP | Between network and link | IP → MAC on one link | Link layer needs MACs but you only know IPs | 1 |
| DHCP | Application | Automatic IP, mask, gateway, DNS | Addresses depend on location | 1 |
| Ethernet / Wi-Fi | Link | Frames on one hop | Delivery between neighbouring devices | 1 |
| OSPF / BGP | Network (control plane) | Build forwarding tables | Routers need to learn the topology | later |
| NAT | Network function | Share one public IP | IPv4 address shortage | 1 |

## 12. Questions you didn't ask

**Why does IP not guarantee delivery?** Guarantees are costly. Keeping the core simple lets routers be fast, and apps that want reliability (TCP) add it at the edges, while apps that don't (video calls) skip the cost.

**Why do we need MAC addresses if IP addresses exist?** They serve different jobs. IP addresses are hierarchical and reflect location, so routers can summarize millions of addresses in one prefix entry. MACs are flat, so a table of every MAC on Earth would be unusable. MACs only need to be unique on one link.

**Does the reply take the same path back?** Not necessarily. Each direction is routed independently, so paths can be asymmetric.

**What if a queue is full?** The datagram is dropped. TCP notices the missing ACK, resends, and slows down. That is how the network tells senders to slow down without anyone sending a message.

**Why not circuit-switch the whole thing?** Computer traffic is bursty. Reserving a dedicated path wastes capacity during every pause, while packet switching lets many flows share the same links.

**What does the ISP see with HTTPS?** The destination IP address, since routers need it. It doesn't see the page contents, because TLS encrypts those.

**Why can your phone's IP change but its MAC doesn't?** The IP says *where* you are on the network. The MAC identifies the hardware.



A: The explanation you received contains a lot of useful information, but your criticism is justified. Its main weakness is that it explains networking as a collection of facts rather than building a mental model of how the whole system operates.

It also contains some oversimplifications and a few technical inaccuracies. For example, DNS does not always use UDP, TCP and TLS do not always require separate handshakes in the way described, and the claim that every hop necessarily incurs all four delays needs qualification.

A better approach is to start with one concrete event, follow the data through the actual software and hardware, explain why each operation exists at the moment it becomes necessary, and only then generalize.

# The journey of a web request: from your browser to a server and back

## 1. First, understand what we're trying to accomplish

Imagine opening your browser and visiting `bhara.com`.

You type the address, press Enter, and the website appears. It feels like one action. Underneath, however, several independent systems cooperate to make it happen.

Your browser needs to find the server. Your computer needs to know how to reach it. Your data must travel across potentially thousands of kilometres of cables and optical fibres. Intermediate devices must forward that data without needing to understand the website itself. The server must receive the request, process it, and send the response back.

Let's use this simplified network:

Your laptop

Browser + operating system + Wi-Fi adapter

Wi-Fi radio signals

Home router

Wi-Fi access point + IP router + often NAT

ISP network → Internet routers → destination network

Web server

Network hardware + operating system + web application

The response travels back through a potentially different route.

We'll initially assume a conventional HTTPS connection using TCP and IPv4. That gives us a concrete example. Later, we'll examine what changes with IPv6, HTTP/3, and other technologies.

One important distinction: this is not necessarily one physical server. A website may sit behind a CDN, a load balancer, a reverse proxy, or several application servers. We'll start with the simpler case and introduce those complications when useful.

## 2. The mental model that makes everything else easier

Before following the request, understand these five jobs.

Application — What are we asking for?

The browser and server agree on the meaning of the message. For example, “Give me the homepage.” HTTP defines the request and response format.

Transport — Which conversation, and how should data be delivered?

TCP identifies the relevant connection, tracks bytes, handles retransmission, and controls how much data is sent.

Network — Which machine should receive this packet?

IP supplies source and destination addresses. Routers use the destination to choose where to forward packets.

Link — How do I get the data to the next device?

Ethernet and Wi-Fi provide mechanisms for sending frames across a particular local link.

Physical — How are bits represented and transmitted?

Electrical signals, light pulses, or radio waves carry the encoded bits.

These are the five layers of the Internet model we'll use throughout this explanation. Real implementations don't always fit perfectly into five neat boxes, but the model is useful for understanding responsibilities.

The critical idea is that each layer solves a different problem and relies on the layer below it to carry its data.

Why divide the work like this? Because your browser shouldn't need to know how a fibre-optic transceiver works, and a router shouldn't need to understand how a web page is rendered.

That separation lets independently developed applications, operating systems, network equipment, and communication technologies work together.

## 3. Who actually implements these layers?

The layers are a conceptual model, not five physical boxes inside your laptop. Their responsibilities are implemented by different combinations of software and hardware.

|Layer|Typical implementation|What it actually does|
|---|---|---|
|Application|Browser, web server, DNS resolver software|Creates and interprets application messages|
|Transport|OS networking stack, or sometimes user-space networking libraries|Manages connections, ports, reliability, and delivery|
|Network|OS IP stack; router forwarding hardware and software|Addresses and forwards IP packets|
|Link|NIC, Wi-Fi adapter, switch hardware, drivers|Sends and receives frames on a link|
|Physical|Radio circuitry, Ethernet PHY, optical transceivers|Transmits and receives signals|

A few distinctions matter here.

- The kernel is the privileged core of an operating system. It typically implements TCP/IP, manages network interfaces, and provides sockets to applications.
    
- A network interface card (NIC) connects a device to a network. It may be integrated into the motherboard or exist as a separate adapter. It handles some operations in hardware.
    
- A network driver is software that allows the operating system to control and communicate with the NIC.
    
- A router connects IP networks. It receives packets on one interface and forwards them through another.
    
- A switch connects devices within a local network and normally forwards Ethernet frames using MAC addresses.
    
- An access point connects wireless devices to a Wi-Fi network. Your home router often combines the access point, Ethernet switch, IP router, and other functions in one box.
    

Why distinguish hardware from software? Because the logical task and the physical implementation are not the same thing. TCP is a protocol, not a particular chip. IP forwarding is a function that may be implemented in specialized silicon rather than by executing every operation on a general-purpose CPU.

Also, the transport layer is not exclusively implemented in the kernel. TCP usually is, but QUIC—the transport protocol used by HTTP/3—is commonly implemented in user-space software, with UDP and IP services provided by the operating system.

## 4. Before sending anything: how does the laptop know where to go?

Your browser cannot immediately send an HTTP request to `bhara.com`. It first needs a destination address and a way to reach that destination.

Three mechanisms are particularly important: DHCP, DNS, and ARP or its equivalent.

### 4.1 DHCP: getting network configuration

When your laptop joins your home network, it needs an IP address and some basic network configuration.

Your router commonly runs a DHCP server that leases this information to your laptop.

A typical IPv4 DHCP exchange involves four messages:

1. Discover: the laptop searches for a DHCP server.
    
2. Offer: a server proposes a configuration.
    
3. Request: the laptop requests the offered configuration.
    
4. Acknowledge: the server confirms the lease.
    

This is often called DORA.

The configuration may include:

- An IP address, such as `192.168.1.20`.
    
- A subnet mask or prefix length, such as `/24`.
    
- A default gateway, such as `192.168.1.1`.
    
- DNS resolver addresses.
    

Why does the laptop need these?

Its IP address identifies it on the local IP network. The subnet prefix helps it decide whether a destination is local. The default gateway provides a next step for destinations outside the local network. The DNS resolver helps it translate domain names into IP addresses.

DHCP is not necessarily involved every time you visit a website. The lease may already exist from when you joined the network, and a device can also use static configuration or IPv6 configuration mechanisms.

### 4.2 DNS: finding the destination IP address

You type:

`https://bhara.com/`

The browser needs to find an IP address associated with `bhara.com`.

DNS provides a distributed naming system. It allows people to use stable, readable domain names without memorizing numerical IP addresses.

The lookup may be satisfied by a browser cache, operating-system cache, local resolver, or recursive DNS resolver. If no usable answer is cached, the resolver may need to query other DNS servers.

A simplified lookup looks like this:

Browser / operating system

Do I already know the address?

Recursive DNS resolver

Checks its cache; looks up the answer if needed

DNS hierarchy, if required

Root → TLD → authoritative DNS, simplified

An IP address is returned

For example, a hypothetical 203.0.113.40

The address above is an illustrative example, not a claim about Bhara's actual server.

Why not use domain names for routing? Routers need an address format suitable for forwarding decisions across networks. DNS separates the human-readable naming system from the IP addressing system.

Why can DNS results change? A domain can point to different servers, use multiple addresses, or direct users to geographically or operationally appropriate infrastructure. Cached records are governed partly by their time to live (TTL).

Why does DNS often use UDP? Ordinary DNS queries are small, and UDP avoids a transport connection handshake. But DNS can also use TCP, including for responses that require it. DNS can additionally use encrypted transports such as DNS over TLS or DNS over HTTPS.

Important: DNS doesn't deliver the web page. It helps discover where to contact the service. The actual web request is a separate exchange.

### 4.3 ARP: finding the next device's MAC address

Suppose the laptop has learned that:

- Its own IPv4 address is `192.168.1.20`.
    
- Its default gateway is `192.168.1.1`.
    
- The website resolves to a public IPv4 address.
    

The destination is outside the laptop's local subnet, so the laptop sends the packet to its default gateway.

But Ethernet or Wi-Fi transmission on the local network needs a link-layer destination. The laptop knows the gateway's IP address, but it may not know the gateway's MAC address.

For IPv4, ARP resolves a local IP address to a link-layer address.

The laptop broadcasts a question equivalent to:

“Who has `192.168.1.1`? Tell `192.168.1.20`.”

The gateway responds with its MAC address, which the laptop can cache.

Why does the laptop need the gateway's MAC instead of the website server's MAC? Because the laptop can communicate directly only with devices on its local link. It cannot use its own local Ethernet or Wi-Fi frame to address an arbitrary remote server several networks away.

ARP requests are confined to the local broadcast domain; routers do not forward them across the Internet.

For IPv6, the corresponding mechanism is Neighbor Discovery, which uses ICMPv6 rather than ARP.

## 5. Establishing a secure conversation

Now the laptop knows the destination IP address. What happens next depends on the protocol being used.

In our example, assume HTTPS over TCP.

### 5.1 TCP establishes a connection

TCP normally begins with a three-way handshake:

Laptop

Server

SYN

Requests a connection

SYN-ACK

Acknowledges and responds

ACK

Acknowledges the response

Why do this?

TCP needs to establish connection state at both endpoints. The handshake synchronizes initial sequence numbers and confirms that the endpoints can exchange TCP segments in both directions.

Those sequence numbers let TCP identify which bytes have been received, which are missing, and how to reconstruct the original byte stream. TCP also maintains state and buffering information for the connection.

A TCP connection is identified by the combination of source IP, source port, destination IP, and destination port, along with the relevant protocol context.

Why use ports? An IP address gets data to a host, but that host may run many applications and maintain many simultaneous conversations. Ports help the operating system deliver incoming transport data to the appropriate socket.

For example, the browser might use a temporary source port such as `52314`, while the server listens on port `443` for HTTPS over TCP.

The exact source port is illustrative.

### 5.2 TLS protects the conversation

TCP provides reliable transport, but it does not by itself encrypt the content or establish the website's identity.

TLS adds security to the communication.

During a typical TLS 1.3 handshake, the client and server negotiate cryptographic parameters, establish shared session keys, and authenticate the server using its certificate and the associated trust chain. The browser checks that the certificate is valid for the intended hostname, among other checks.

After the handshake, TLS encrypts and authenticates application data.

Why is this necessary? Without TLS, someone who can observe or modify an unprotected connection may read or tamper with sensitive information. With HTTPS, the contents of the HTTP exchange are protected against ordinary on-path interception.

One important correction to the original explanation: TCP and TLS do not have a fixed number of round trips in every situation. A new TCP connection typically needs one round trip for its handshake. A typical full TLS 1.3 handshake adds another round trip before normal application data can be sent. Resumption and other protocol details can change this.

TLS 1.3 also supports limited early data in some resumed connections, with replay risks that applications must consider.

And HTTPS does not necessarily mean TCP. HTTP/3 uses QUIC, which provides transport functions over UDP and integrates the TLS 1.3 handshake into its connection establishment.

## 6. The central event: the browser sends a request

We have now reached the part where the layers become easiest to understand.

Imagine the browser wants the homepage. At a conceptual level, it creates an HTTP request similar to this:

```
GET / HTTP/1.1
Host: bhara.com
Accept: text/html
```

This is a simplified example. A real request may include many additional headers, cookies, compression preferences, and other fields. Modern HTTP/2 and HTTP/3 also use different wire formats from this textual HTTP/1.1 example.

The important point is that the browser creates a message with a particular meaning. It then relies on the networking stack to carry that message to the server.

### 6.1 Application layer: creating the message

The browser determines what resource to request and creates the appropriate HTTP message.

Why does HTTP exist? The client and server need an agreed way to express requests, responses, methods, resource paths, status codes, metadata, and other application-level information.

For example:

- `GET` indicates a request to retrieve a representation of a resource.
    
- `/` identifies the requested path.
    
- `Host` identifies the intended website in HTTP/1.1, which matters when many websites share one IP address.
    

Cookies may carry session identifiers, allowing the server to associate requests with a logged-in user. HTTP itself does not automatically maintain the entire concept of a user session.

The browser then sends the HTTP data through its networking interface, typically using a socket.

A socket is an operating-system programming interface for communication. It is not a physical cable or a separate network layer. It allows an application to request operations such as connecting, sending data, and receiving data.

With ordinary browser networking, the browser's networking process may communicate with the OS kernel, which manages TCP and IP. The exact process boundaries depend on the browser and operating system.

### 6.2 Transport layer: preparing the data for delivery

For our TCP example, the operating system takes the application data and creates TCP segments.

Conceptually:

Application data

HTTP request

TCP segment

TCP header

Application data

The application data is TLS-protected in HTTPS; the diagram omits that extra structure.

The TCP header contains information such as source and destination ports, sequence and acknowledgment numbers, flags, and a checksum.

Why is each necessary?

- Ports: direct the data to the appropriate connection and application socket on the receiving host.
    
- Sequence numbers: allow the receiver to reconstruct the byte stream and identify missing data.
    
- Acknowledgments: tell the sender which data has been received.
    
- Flags: indicate control information, such as connection establishment or closure.
    
- Receive window: advertises the receiver's available capacity for incoming data.
    
- Checksum: helps detect corruption in the TCP segment.
    

TCP also manages retransmissions, flow control, and congestion control. It keeps track of outstanding data and retransmits when its recovery mechanisms indicate that data has likely been lost.

Why not send the entire response as one enormous TCP unit? Network links have limits on how much data can fit into a packet or frame. TCP divides a byte stream into manageable segments based on the path's maximum segment size and other constraints.

Your initial request may be small enough to fit into a single segment, but that is not guaranteed. The response may contain far more data.

A subtle but important distinction: TCP provides a byte stream, not a sequence of application messages. If the browser writes data in two separate operations, the server is not guaranteed to receive two corresponding chunks. TCP reconstructs the ordered stream; HTTP determines how to interpret it.

### 6.3 Network layer: deciding the destination

The operating system's IP stack adds an IP header to the transport data.

Conceptually:

IP packet (IPv4 example)

IP header

TCP segment

The IP header contains source and destination IP addresses, among other fields.

The IP header provides information such as:

- Source and destination IP addresses.
    
- The protocol identifier indicating what is carried inside the packet.
    
- Packet length and, for IPv4, a header checksum.
    
- TTL in IPv4, or Hop Limit in IPv6.
    

Why does IP exist when TCP already has addresses and ports? TCP ports identify endpoints of a transport conversation on a host, whereas IP provides addressing and forwarding across interconnected networks.

The IP protocol field in IPv4 identifies the encapsulated protocol, such as TCP or UDP. It is distinct from a TCP port, which identifies an application endpoint within the transport system.

Why is there a TTL or Hop Limit? Packets can encounter routing loops. Each router decreases this value; if it reaches zero, the packet is discarded. This prevents packets from circulating indefinitely.

The operating system then consults its routing table to determine where to send the packet.

For a remote destination, that will commonly be the default gateway.

### 6.4 Link layer: preparing the next hop

The laptop now has an IP packet ready to send. It needs to place that packet into a frame suitable for the local network.

For a simplified Ethernet example:

Ethernet frame

Link header

IP packet

FCS

FCS is the frame check sequence, used for link-level error detection.

The link header contains link-layer addressing and information about the payload format. The frame check sequence in Ethernet helps detect transmission errors.

Why does the frame need a destination MAC address? The local link needs a way to identify which device should receive a transmitted frame.

For our remote website, the destination MAC is normally the gateway's MAC address—not the website server's MAC address.

This is the distinction to remember:

- IP destination: the remote endpoint the packet is intended to reach.
    
- Link destination: the next device receiving the frame on this particular link.
    

Wi-Fi uses its own frame format and has additional mechanisms for sharing the radio channel and handling wireless transmission. Its frames are not simply Ethernet frames transmitted over radio.

The OS and NIC cooperate to send the frame. Depending on the operating system, driver, and hardware, some of the framing and transmission work is handled in software, some in hardware, or both.

### 6.5 Physical layer: transmitting the bits

Finally, the link hardware transmits the encoded data as signals.

On Wi-Fi, the radio hardware converts encoded bits into radio transmissions. On Ethernet, electrical or optical signaling carries the information through the medium.

Why is this separate from IP or HTTP? Because the same higher-level protocols can operate over different physical media. The browser does not need to know whether the data will travel over copper, fibre, or radio.

At this point, the laptop has done its part. The frame enters the local network, and the next device takes over.

## 7. What happens at the home router?

The router receives the laptop's frame. This is where the distinction between a device and a link becomes particularly important.

A router does not normally forward the laptop's original Wi-Fi frame all the way to the remote server. It receives a frame on one interface, extracts the IP packet, decides how to forward it, and transmits the packet in a new frame on the outgoing interface.

Follow the sequence.

1. Receive the frame. The Wi-Fi interface receives radio signals and reconstructs the frame. Link-level error detection and other checks may identify an invalid frame, which is discarded.
    
2. Extract the IP packet. The router processes the frame and makes its IP payload available to its forwarding logic.
    
3. Inspect the IP header. It examines the destination address and other relevant fields, including the TTL or Hop Limit.
    
4. Apply forwarding rules. It consults its forwarding information to select an outgoing interface and next hop. Policy rules, firewall rules, and NAT may also apply.
    
5. Queue the packet if necessary. If the outgoing interface is busy, the packet may wait in a queue. If the queue is full, it may be dropped.
    
6. Transmit on the outgoing link. The router constructs a suitable frame for that link, including the appropriate link-layer destination, and transmits it.
    

Why is the frame rebuilt? The outgoing interface may use a different link technology, and the next hop has its own link-layer address. The original frame was only meaningful on the original link.

In ordinary IP forwarding, the source and destination IP addresses remain unchanged across routers. The TTL or Hop Limit changes at each forwarding hop, and the IPv4 header checksum must be updated accordingly.

However, NAT can modify addresses and ports, and tunnelling or other network mechanisms can add additional headers. The unchanged-IP rule is therefore the normal case, not an absolute law.

### 7.1 NAT: why your private address may be rewritten

Your laptop may use a private IPv4 address such as `192.168.1.20`. Your router may have a public IPv4 address assigned by the ISP.

With IPv4 network address and port translation (NAPT, commonly called NAT), the router can rewrite the packet's source IP address and often its source port, recording a mapping so that reply traffic can be associated with the correct internal connection.

Why? IPv4 has a limited address space, and private networks can reuse the same private address ranges. NAT allows multiple devices to share a public IPv4 address.

NAT is common, but it is not a required step for all Internet communication. IPv6 networks can often route globally unique addresses without this form of address translation, although firewalls may still restrict incoming traffic.

NAT also means the router may inspect and modify transport-layer fields. This is one reason real networks don't always map perfectly onto the clean boundaries shown in a textbook diagram.

### 7.2 What about switches and ISP routers?

A switch and a router have different primary responsibilities.

An Ethernet switch typically forwards frames within a local network using a MAC-address table. It learns which source MAC addresses appear on which ports and uses that information to select outgoing ports.

An IP router forwards packets between networks using IP routing and forwarding information.

A home device may combine several of these functions, but the jobs remain conceptually distinct.

As the packet travels through the ISP, it may pass through many routers. Each router makes a forwarding decision based on its own routing information. No single router needs to know the complete route from your laptop to the server.

The routing system determines which routes are available, while the forwarding system uses the resulting forwarding information for individual packets.

Protocols such as OSPF and IS-IS can distribute routing information within networks. BGP is used to exchange reachability information between autonomous systems. They are control-plane protocols, not part of the HTTP request itself.

Why separate routing from forwarding? A router needs to make packet-forwarding decisions rapidly, while discovering, selecting, and updating routes is a separate process that operates as network conditions change.

## 8. What happens when the packet reaches the server?

Eventually, the packet reaches the destination network. The last router sends it across the final link to the server, using a frame appropriate for that link.

The server reverses the receiving side of the process.

1. Network interface

Receives signals and reconstructs frames

2. Link processing

Checks relevant frame information and extracts the IP packet

3. IP processing

Checks addressing and dispatches the packet to the indicated transport protocol

4. TCP processing

Validates the segment, tracks sequence numbers and reconstructs the byte stream

5. TLS and web application

Decrypts and authenticates data, then interprets the HTTP request

The precise division of work depends on the operating system, hardware offloading, and server architecture, but this is the logical sequence.

### 8.1 The OS delivers data to the correct connection

The server's IP stack identifies the appropriate transport protocol. TCP then checks the segment and uses the connection's addressing and port information to identify the relevant connection state and socket.

Why isn't the destination port enough on its own? Many clients can connect to the same server port simultaneously. The server may have thousands of connections to port 443, each with different remote endpoints.

TCP validates the data using its checksum and uses sequence numbers to handle missing, duplicate, or out-of-order segments. It acknowledges received data according to TCP's acknowledgment rules.

If data arrives out of order, TCP can retain it until the missing bytes arrive. The application normally receives the reconstructed stream in order.

The NIC may use interrupts, polling, or a combination of techniques to notify and coordinate with the operating system. It is not necessarily the case that every incoming frame triggers a separate CPU interrupt.

### 8.2 TLS decrypts the application data

The TLS implementation uses the negotiated session keys to authenticate and decrypt the protected data.

Why decrypt before interpreting HTTP? The web application needs the actual HTTP request, but on the wire that request is protected by TLS.

In a common server architecture, TLS processing is handled by the web server or a reverse proxy. If TLS terminates at a load balancer or CDN, that component decrypts the connection and forwards the request to another server over a separate connection or protocol.

### 8.3 The web server processes the request

The web server receives the HTTP request and interprets its method, path, and headers.

It may:

- Return a static HTML file.
    
- Execute application code.
    
- Query a database.
    
- Call another service.
    
- Check authentication and authorization.
    
- Generate a response based on the requested resource.
    

The server constructs an HTTP response, for example:

```
HTTP/1.1 200 OK
Content-Type: text/html
```

The actual response also includes a body or other content as appropriate.

Why does the web server need HTTP? The network stack can transport bytes, but it cannot decide what those bytes mean as a website request. HTTP gives the application a shared interpretation.

One important point: the server's application processing time is separate from the four classic packet-network delays. A slow database query can delay the page even if every network link is fast.

## 9. The response travels back

The server sends the response through its networking stack. Conceptually, the process now runs in the other direction.

For a TCP connection, the response bytes are protected by TLS and passed into TCP. TCP divides the stream into segments, and IP encapsulates those segments into packets. The link layer frames the packets for transmission across the server's local link.

Routers forward the packets toward the client. At the client, TCP reconstructs the stream, TLS decrypts and authenticates the data, and the browser interprets the HTTP response.

Why divide a large response into multiple segments? The network imposes limits on packet sizes, and sending manageable units lets TCP control the amount of outstanding data and recover from losses without retransmitting the entire response.

Why might packets arrive out of order? Different packets can encounter different queueing conditions, paths, or delays. IP itself does not promise ordered delivery.

TCP reassembles the stream in the correct order before exposing it to the application.

Two separate mechanisms help control the flow of data:

- Flow control: protects the receiver from being overwhelmed. The receiver advertises its available buffer capacity.
    
- Congestion control: helps prevent the sender from overwhelming the network. TCP adjusts its sending behaviour based on signals such as acknowledgments, loss, and, in some algorithms, delay.
    

These solve different problems. A server might be able to process data quickly but still be limited by congestion on a network link. Conversely, the network may have plenty of capacity while a slow client cannot accept data quickly enough.

Finally, the browser processes the response, parses HTML, discovers additional resources such as CSS, JavaScript, fonts, and images, and may issue more requests. Downloading the initial HTML is therefore often only the beginning of loading a page.

## 10. The four types of network delay

This is one of the most important concepts to understand because it explains why a request can be slow even when its destination is known and its packets are relatively small.

For a simple packet traversing a link and its associated forwarding device, the four classic delays are:

1. Processing delay

Time spent examining the packet, checking relevant fields, and determining what to do next.

Where: host networking stacks, routers, and other packet-processing devices.

2. Queueing delay

Time spent waiting for an outgoing interface or another processing resource to become available.

Where: router output queues, network interface queues, and other congested resources.

3. Transmission delay

Time required to put all the packet's bits onto the link.

Where: the transmitting side of each link, including NICs and router interfaces.

4. Propagation delay

Time taken for the signal to travel through the physical medium.

Where: the cable, fibre, or radio path between the transmitter and receiver.

### 10.1 Processing delay: examining the packet

Suppose a router receives a packet. It may need to validate relevant header fields, decrement the TTL, consult its forwarding table, and apply any configured rules.

The time taken to perform these operations contributes to processing delay.

Why does it matter? Because every forwarding device needs some time to handle the data. In well-functioning high-speed networks, this can be very small, but it is not necessarily zero.

Processing delay can also increase when a device is overloaded or when its packet-processing path is complex.

### 10.2 Queueing delay: waiting for a turn

Suppose your laptop sends data over a connection that shares a limited-capacity link with other traffic.

Even if your packet is ready to transmit, another packet may already be using the outgoing interface. Your packet waits in a queue.

The time it waits is queueing delay.

Why does this matter so much? Because queueing delay depends on how much traffic is competing for a resource and how quickly that resource can serve it.

A lightly loaded link may have almost no queueing delay. A congested link may have substantial queueing delay, and an overflowing queue may drop packets altogether.

This is why an Internet connection can have low latency when idle but much higher latency while a large upload is running.

### 10.3 Transmission delay: putting bits onto the link

Transmission delay depends on the packet's size and the link's transmission rate.

\[ d_{\text{transmission}}=\frac{L}{R} \]

Here:

- \(L\) is the number of bits being transmitted.
    
- \(R\) is the link's transmission rate in bits per second.
    

Consider a 1,500-byte packet transmitted over a 10 Mbps link.

\[ \frac{1500\times8}{10{,}000{,}000} =0.0012\text{ s} \]

That is 1.2 milliseconds to put those 12,000 bits onto the link, ignoring framing and other overhead.

On a 1 Gbps link, the same 1,500-byte packet requires approximately 12 microseconds.

Why does transmission delay occur at every link? Each transmitting interface must serialize the packet's bits onto its outgoing link. The next router cannot transmit those same bits onto the next link without receiving them first, although real systems can pipeline operations and use different forwarding behaviours.

### 10.4 Propagation delay: distance matters

Propagation delay is the time taken for a signal to travel from one end of a link to the other.

\[ d_{\text{propagation}}=\frac{\text{distance}}{\text{signal speed}} \]

Signals in optical fibre travel at roughly \(2\times10^8\) metres per second.

For a 1,000 km fibre path:

\[ \frac{1{,}000{,}000}{200{,}000{,}000} =0.005\text{ s} \]

That is approximately 5 milliseconds one way, assuming the full path length is 1,000 km.

Why doesn't a faster Internet subscription eliminate this delay? Increasing bandwidth lets a link transmit bits more quickly, but it does not make the signal travel through the same physical distance instantaneously.

Actual Internet paths may be longer than the geographic straight-line distance, and real latency also includes forwarding, queueing, access networks, and endpoint processing.

### 10.5 Putting the four delays together

For a simplified store-and-forward network, the total delay across several links can be modelled as:

\[ d_{\text{total}}= \sum_{i=1}^{N} \left( d_{\text{proc},i}+ d_{\text{queue},i}+ d_{\text{trans},i}+ d_{\text{prop},i} \right) \]

Here \(N\) is the number of links being traversed, and the terms represent the relevant delays along the path.

This is a conceptual model, not a complete formula for page-load time. Real networks can pipeline transmission, use cut-through forwarding, batch packet processing, and introduce additional delays from retransmissions and endpoint work.

Also, these delays don't all happen in the same way at every device. A switch, access point, router, and host have different responsibilities. The classic four-delay model is most directly applied to packet forwarding across links.

### 10.6 Which delay dominates?

|Situation|Likely important delay|Reason|
|---|---|---|
|Long-distance connection|Propagation|The signal must travel a long physical distance|
|Congested home uplink|Queueing|Traffic is waiting for limited outgoing capacity|
|Low-bandwidth link|Transmission|Bits take longer to serialize onto the link|
|Busy router or host|Processing|Packet handling competes for processing resources|

There is no universal rule that one type always dominates.

And one final distinction: bandwidth, throughput, and latency are not interchangeable.

- Bandwidth is the link's nominal transmission capacity.
    
- Throughput is the rate at which data is actually delivered.
    
- Latency is the time taken for data to travel between endpoints or through a system.
    

A connection can have high bandwidth and still have high latency.

## 11. Who checks what, and why do we need so many checks?

You might wonder why the same data is checked multiple times. Isn't one checksum enough?

No, because different checks protect against different problems.

|Component|What it checks or handles|Why|
|---|---|---|
|Receiving link hardware|Link-specific error detection, such as Ethernet FCS|Detect corruption on that link|
|Router's IP forwarding logic|Destination address, TTL/Hop Limit, forwarding rules|Forward packets correctly and prevent indefinite loops|
|Receiving host's IP stack|IP processing and dispatch to the correct protocol|Deliver the packet to the intended local network stack|
|Receiving TCP|TCP checksum, sequence numbers, acknowledgments, connection state|Detect corruption, recover from loss, and reconstruct the byte stream|
|TLS implementation|Authentication tags and session security|Detect tampering and protect confidentiality|
|Web server|HTTP syntax, application rules, authentication and authorization|Process valid requests and enforce access rules|

Notice that these checks do not all perform the same job.

An Ethernet FCS detects many transmission errors on a single link, but it does not provide end-to-end delivery guarantees. Once a router has received a frame and extracted its IP packet, it creates a new frame for the next link.

TCP's checksum protects the TCP segment against accidental corruption, but it is not a cryptographic integrity mechanism. TLS provides cryptographic authentication of protected data.

Likewise, TCP can deliver bytes reliably without knowing whether an HTTP request is authorized. That decision belongs to the application.

The broader principle: each layer checks the things it is responsible for. Lower layers can help detect or recover from local failures, while the endpoints handle end-to-end communication requirements.

## 12. The protocols involved, organized by purpose

A protocol is an agreed set of rules for message formats, communication order, and how participants respond to different events.

Here are the important protocols in our example.

|Protocol|Layer or role|Why it exists|
|---|---|---|
|HTTP|Application|Defines web requests and responses|
|TLS|Security layer above transport|Encrypts and authenticates communication|
|DNS|Application|Maps domain names to resource records, including IP addresses|
|TCP|Transport|Provides a reliable, ordered byte stream|
|UDP|Transport|Provides datagrams without TCP's connection and retransmission machinery|
|IP|Network|Addresses and forwards packets between networks|
|ICMP / ICMPv6|Network support|Carries error reports and diagnostic messages|
|ARP|IPv4 local-network support|Resolves local IPv4 addresses to link-layer addresses|
|Neighbor Discovery|IPv6 support|Provides local address resolution and other neighbour functions|
|DHCP|Network configuration|Automatically provides configuration such as an IPv4 address and gateway|
|Ethernet / Wi-Fi|Link|Transmits frames across local links|
|OSPF / IS-IS / BGP|Routing control plane|Distributes and selects routes|
|NAT|Network function, not a standalone protocol|Translates addresses and sometimes ports|

Not every protocol participates in every request.

For example, DHCP is generally involved when obtaining or renewing configuration, not each time a page is opened. DNS may be skipped if the answer is cached. ARP may be skipped if the required mapping is already in the neighbour cache.

Similarly, OSPF and BGP help routers learn routes, but they are not exchanged between your browser and the web server as part of the HTTP request.

### What changes with HTTP/3?

HTTP/3 is an important exception to the simple TCP-based journey described above.

With HTTP/1.1 or HTTP/2 over HTTPS, TCP normally provides transport, with TLS securing the connection. HTTP/3 instead runs over QUIC, which uses UDP as its underlying transport.

QUIC implements reliability, congestion control, connection management, and encryption-related integration above UDP. It also supports multiple independent streams within a connection, reducing certain kinds of cross-stream blocking associated with TCP.

This means the conceptual jobs still exist, but the implementation does not always match the neat five-layer diagram one-to-one.

## 13. Additional questions worth understanding

### Why don't routers guarantee that every packet reaches its destination?

Routers operate under resource constraints. Links fail, queues overflow, routes change, and packets may be lost.

Requiring every router to provide full reliable delivery would make the network substantially more complex. Instead, IP provides a basic packet-delivery service, while protocols such as TCP implement reliability at the endpoints.

This does not mean routers do nothing to improve reliability. Networks use redundancy, alternate routes, error detection, and other mechanisms. It means IP itself does not promise that every packet will arrive.

### Why are MAC addresses necessary if IP addresses already exist?

IP addresses identify network-layer destinations, while MAC addresses support delivery across particular link-layer networks.

The laptop needs the gateway's MAC address to transmit a local frame. The gateway then forwards the IP packet over another link, using the addressing appropriate for that link.

This separation allows the Internet to connect networks with different link technologies without requiring every device to understand every other link's framing rules.

### Does the response take the same route as the request?

Not necessarily.

IP routing decisions are made independently in each direction. The response might travel through a different sequence of routers because of routing policies, topology, or changing network conditions.

TCP still maintains the connection's endpoint identity even when packets take different paths.

### What happens if a router's queue becomes full?

The router may drop packets.

TCP can infer loss from mechanisms such as duplicate acknowledgments or retransmission timeouts, retransmit data, and adjust its congestion-control behaviour.

However, not every packet loss causes an immediate retransmission, and not every network protocol responds to loss the same way. UDP applications, for example, must implement their own recovery mechanisms if they need them.

### What can the ISP see when you use HTTPS?

The ISP can generally observe network-level metadata such as your IP address, the destination IP address, traffic timing, and data volume. Depending on the DNS method and other protocol details, it may also be able to observe DNS queries or some connection metadata.

HTTPS protects the contents of the HTTP exchange, so an ordinary network observer cannot simply read the page body, cookies, or passwords from the encrypted connection.

HTTPS does not hide every aspect of a connection. The exact metadata visible depends on the protocols and configuration in use.

### Why does opening a website often require many requests?

The HTML document may reference CSS, JavaScript, images, fonts, API endpoints, and other resources. The browser discovers these references and requests the necessary resources.

Modern browsers can reuse connections, cache resources, and download some resources concurrently. Consequently, loading a website is often a sequence of overlapping operations rather than one request followed by one response.

### Why do content delivery networks exist?

A CDN places cached content or application infrastructure closer to users. This can reduce the distance data travels, lower latency, reduce load on an origin server, and improve resilience.

It cannot eliminate all latency, and dynamic requests may still need to reach an origin or another backend service. But moving frequently accessed content closer to users can substantially improve performance.

### Is all of this necessary just to display one page?

The browser and networking stack perform much of this work automatically. You do not need to configure each header or manually forward packets.

But understanding the responsibilities helps explain why a failure can occur at different places: DNS may fail before a connection begins, TCP may time out, TLS may reject a certificate, a router may drop packets, or the web application may return an error.

## 14. The complete mental model

Here is the process reduced to its essential sequence.

1. Identify the destination

DNS resolves the domain name to an IP address. The operating system determines how to reach that address using its routing table.

2. Establish secure communication

TCP establishes a connection in the conventional case. TLS negotiates security and authenticates the server.

3. Create and encapsulate the request

HTTP defines the request. TLS protects it. TCP provides transport, IP provides network addressing, and the link layer prepares a frame for the next device.

4. Forward the packet across networks

Each router receives a frame, processes its IP packet, selects a next hop, and transmits a new frame over the outgoing link.

5. Process the request at the server

The receiving stack delivers the data to the appropriate connection. TLS decrypts it, and the web server interprets the HTTP request and generates a response.

6. Return the response

The server sends data back through the networking stack. Routers forward it toward the client, which reconstructs, decrypts, and interprets the response.

7. Render the page

The browser processes the returned content and may request additional resources before the page is fully loaded.

The single most useful principle to retain is this:

The application defines the meaning of the data, transport manages the conversation, IP moves packets between networks, the link layer moves frames between neighbouring devices, and the physical layer carries the bits.

The layers cooperate because each solves a different problem. The reason for headers, addresses, checksums, routing decisions, and retransmissions becomes clearer once you understand which problem each mechanism solves.

You do not need to memorize every header field now. Instead, when you encounter a new field or protocol, ask three questions:

1. What problem does this solve?
    
2. Which component needs this information, and at what point?
    
3. What would fail, or become more complicated, if this mechanism did not exist?
    

Those questions will help you develop a working mental model rather than merely remembering a list of networking terms.


Q: 1. Why Application is “What are we asking for?”?
2. Why do the browser and server need to agree on the meaning of the message? What does agreeing mean here?
3. Is HTTP the protocol that defines the format and order of a message (request/response)?
4. Transport — Which conversation. Here, what do you mean by “which conversation”?
5. You again introduced TCP without describing it first. How can the readers follow a simple flow if they are bombarded with information they haven’t learned yet in every step?
6. What does relevant connection mean here? Why does tracking bytes and handling retransmission needed? How can the software or device handling TCP know that a packet is lost or haven’t successfully transmitted?
7. Again, what is IP? How can it know the destination address? Is router a device which can determine where to forward the packet next from the destination address?

Don't answer the above mentioned questions now. We'll come back to them again after finishing reading the whole article.

I think the section 2, 3 should come later or written in a different way. It seems like they are telling me things I am yet to explore.

However, let’s read from section 4.

### 4.1

1. I understood that my laptop needs an IP address to communicate with any other networking device on the internet. But why doesn’t it already have an IP address? Why does the router need to hand it one?
2. What do you mean by DHCP server?
3. How or when does the laptop search for a DHCP server?
4. Offer and accept are the two words that goes well. But how can offer be replied with a request? And how can a request be replied with acknowledge, not a response?
5. What is a subnet mask and why is it needed? Why does the laptop needs to know about a destination when it connects to a network? It isn’t sending or receiving any packet yet, right?
6. What is a default gateway? Is it the IP address of the connected router?
7. Are the DNS resolver addresses actually IP addresses of the ISP?
8. What do you mean by static configuration?

### 4.2

1. You mentioned UDP without explaining it, bad experience. I think, even including the question here was unnecessary.

### 4.3

1. The laptop send the packet to its default gateway; the router, fine. But how does it send the packet?
2. Ethernet or Wi-Fi transmission on the local network needs a link-layer destination - why link layer? Again you introduced layers without explaining them or in an intuitive way.
3. Why does it need to know the gateway’s MAC address? Why isn’t IP address enough?

Overall, I understood that in this step browser finds out the IP address of the server and now knows its own devices IP address and the IP address and the MAC address of the connected router. So, it now knows the destination address and the first step to reach that destination (sending packet to router). Am I correct? Have I missed anything?

Look, just answer my questions of section 4, not anything else. The other questions were my reviews. I will read each section and ask such questions for clarification. And from my questions, you will also understand how to write it better. And if you understand that, you can rewrite the discussed section if needed and show it to me.


A: You're right about the sequencing. The previous explanation introduced mechanisms before establishing the problem each mechanism solves. I'll rebuild sections 4.1–4.3 around one question: what must the laptop know before it can send data to a remote server, and how does it learn each thing?

We'll assume a typical home Wi-Fi network using IPv4.

# 4. Before sending a web request

Imagine your laptop has just connected to your home Wi-Fi, and you want to open `bhara.com`.

Before it can communicate with the website, it needs to establish three things:

1. Its own network configuration: how it is identified on the local network and how to reach other networks.
    
2. The server's IP address: where the website can be reached.
    
3. The local next hop: which nearby device should receive the data first.
    

These are separate problems. Let's solve them one at a time.

## 4.1 Getting an IP address and network configuration

### 1. Why doesn't my laptop already have an IP address?

An IP address is not quite like a serial number permanently assigned to a laptop.

Think of it more like an address you use within a particular network. When your laptop connects to a home network, it needs an address suitable for communicating on that network.

For example, your home network might assign your laptop:

`192.168.1.20`

When you connect to a different network, such as Wi-Fi at a university, the appropriate address may be completely different.

Why not permanently assign every laptop one address and use it everywhere?

Because IP addresses are used for network communication and routing. Different networks have different address configurations, and private IPv4 addresses can be reused across different homes. Someone must configure your laptop with an address appropriate for the network it has joined.

There are several ways to do this. One common method is DHCP, which automates the configuration.

A qualification: laptops don't necessarily start without any IP addresses. A laptop may retain a valid configuration, be configured manually, or have an automatically configured IPv6 link-local address. We're considering the common case where it needs to obtain IPv4 configuration for the home network.

### 2. What is a DHCP server?

A DHCP server is a program or network service that provides network configuration to devices requesting it.

DHCP stands for Dynamic Host Configuration Protocol.

In a typical home network, your router runs this service. This does not mean the router has to be a separate DHCP-only device. One physical box can perform several jobs.

For example, your home router may act as:

- A Wi-Fi access point.
    
- A router connecting your home network to the Internet.
    
- A DHCP server assigning network configuration.
    
- A DNS forwarder handling DNS requests on behalf of your devices.
    

These are different functions, even when one physical box performs all of them.

Why have a DHCP server? Without it, someone would have to manually configure the IP address, subnet mask, default gateway, and DNS resolver on every device, or the device would need another configuration mechanism.

DHCP automates that work.

### 3. How and when does the laptop search for a DHCP server?

Usually, after the laptop has successfully joined the Wi-Fi network, its networking software tries to obtain an IPv4 configuration if it doesn't already have a usable one.

But here's the problem: the laptop doesn't yet have an IPv4 address it can use for ordinary network communication. How can it ask for one?

DHCP includes a special initial procedure for exactly this situation.

The laptop sends a DHCP Discover message as a local broadcast. Think of a broadcast as announcing something to the local network rather than sending it to one specific, already-known recipient.

It does not need an Internet connection to do this. The laptop can transmit a message over the local Wi-Fi network without first having a normal, working Internet connection.

The announcement effectively says:

“I'm trying to configure my network. Is there a DHCP server here that can help me?”

A DHCP server listening on that network can receive the message and offer a configuration.

The exact mechanism used to carry the message involves the local network's framing and addressing. We'll get to that in section 4.3. For now, the important idea is that the device has a way to request configuration before it has obtained its ordinary IPv4 address.

### 4. How can an offer be answered with a request, and a request with an acknowledgment?

DHCP commonly uses four messages, often summarized as DORA:

## 1

Discover — The laptop asks for configuration.

“Is there a DHCP server that can configure me?”

## 2

Offer — A DHCP server proposes configuration.

“You could use `192.168.1.20`, with these network settings.”

## 3

Request — The laptop indicates which offer it wants.

“I'd like to use the address and configuration from this offer.”

## 4

Acknowledge — The server confirms the configuration.

“Confirmed. You may use this configuration for the specified lease period.”

Your confusion is reasonable if you read the names as an ordinary conversation. Why would an offer be answered with a request?

The key is that these are names of specific message types in the DHCP protocol, not a requirement that every reply use the opposite word from the preceding message.

An offer is a proposal. A request tells the server which configuration the client wants to use. That matters especially when more than one DHCP server responds: the client can indicate which offer it has selected.

The acknowledgment then confirms the server's decision and the configuration the client may use.

The server is effectively granting a lease for the address. A lease usually has a duration, and the client may need to renew it.

So the exchange is not four arbitrary messages. Each message advances the configuration process:

Find a server → receive a proposal → select an offer → receive confirmation.

### 5. What is a subnet mask, and why does the laptop need it before sending anything?

Let's say DHCP provides these settings:

|Setting|Example|
|---|---|
|Laptop's IPv4 address|`192.168.1.20`|
|Subnet mask|`255.255.255.0`|
|Default gateway|`192.168.1.1`|
|DNS resolver|`192.168.1.1`|

These are illustrative values for a home network.

The subnet mask tells the laptop which part of its IP address identifies the local network and which part identifies a host within that network.

For this example, the mask `255.255.255.0` corresponds to a `/24` prefix. The local network is `192.168.1.0/24`.

You don't need to memorize the binary details yet. What matters is the decision the laptop can make using this information.

Suppose the laptop wants to send something to `192.168.1.35`. That destination belongs to the same local subnet as the laptop.

Now suppose it wants to send something to `203.0.113.40`. That's outside the local subnet in our example.

The laptop needs to treat these situations differently.

- For a destination on the local subnet, it can try to deliver data directly to that destination on the local network.
    
- For a destination outside the local subnet, it generally sends the data to its default gateway, which can forward it toward the remote network.
    

This is why the laptop needs the subnet mask before it starts communicating with the website. It does not need to know the website's destination in advance. It needs the mask so it can make the correct decision whenever a destination becomes known.

The subnet mask and default gateway are configuration for future communication, not evidence that the laptop is already sending a web request.

### 6. What is the default gateway? Is it the router's IP address?

Yes, in a typical home network, the default gateway is the IP address of the router's local-network interface.

For example:

- Laptop: `192.168.1.20`
    
- Home router's local interface: `192.168.1.1`
    
- Website's hypothetical public IP: `203.0.113.40`
    

The laptop can communicate directly with appropriate destinations on its local subnet. When it needs to reach an IP destination outside that subnet, it normally sends the packet to the gateway.

Think of the gateway as the first forwarding device to which the laptop hands traffic intended for other networks.

The laptop doesn't need to know the entire route to the website. It needs to know the first step: where to send the packet locally.

The router then decides how to forward the packet toward its destination.

Strictly speaking, the default gateway is a routing configuration entry identifying a next-hop address. It does not have to be the same physical box that provides Wi-Fi, although home routers commonly combine those functions.

### 7. Are the DNS resolver addresses the IP addresses of the ISP?

They can be, but not necessarily.

A DNS resolver is a service that helps your computer find the information associated with a domain name, such as the IP address for `bhara.com`.

DHCP may provide the IP address of a DNS resolver operated by your ISP. Alternatively, it may provide your home router's local IP address, with the router forwarding DNS queries to another resolver.

You can also configure a resolver operated by a public DNS provider.

For example, if DHCP provides `192.168.1.1` as the DNS resolver, that means the laptop sends its DNS queries to the router's local address. The router may then contact an upstream resolver to obtain the answer.

Why does the laptop need the resolver's address? Because knowing that DNS exists is not enough. The laptop needs to know where to send its lookup request.

### 8. What is static configuration?

Static configuration means that network settings are entered or configured explicitly rather than automatically obtained through a DHCP lease.

For example, someone could manually configure the laptop with:

- IP address: `192.168.1.20`
    
- Subnet mask: `255.255.255.0`
    
- Default gateway: `192.168.1.1`
    
- DNS resolver: `192.168.1.1`
    

The administrator must make sure the settings are appropriate for the network and that the chosen IP address doesn't conflict with another device's address.

Why use DHCP rather than static configuration? DHCP makes configuration easier to manage, especially when many devices connect and disconnect or network settings change.

Static configurations are still useful in some environments. A DHCP server can also be configured to give the same device a consistent address through a reservation; that's related to static addressing but is not the same as manually configuring the device.

## 4.2 Finding the website's IP address

Now the laptop has network configuration: its own address, subnet mask, default gateway, and DNS resolver.

Next comes the website lookup.

When you type `bhara.com`, the browser or its networking components need an IP address corresponding to that domain. A DNS lookup provides that information when it isn't already available from a usable cache.

For this stage, all you need to understand is:

1. The domain name is the human-readable name.
    
2. DNS helps discover the corresponding IP address.
    
3. The laptop uses that IP address when communicating with the website.
    

I am leaving out DNS's underlying transport mechanism here. You don't need that detail to understand why a resolver is needed or what the lookup accomplishes.

Also, DNS may not require a new network exchange every time you visit a site. The browser or operating system may already have a usable cached answer.

## 4.3 Knowing where to send the first packet

Now suppose the laptop has learned the website's IP address:

`203.0.113.40`

Its own configuration is:

- Laptop IP: `192.168.1.20`
    
- Gateway IP: `192.168.1.1`
    
- Website IP: `203.0.113.40`
    

The subnet mask tells it that the website is outside its local subnet. Therefore, the laptop needs to send the data to its default gateway.

But how does it physically send that data to the gateway?

### 1. How does the laptop send the packet?

The laptop cannot simply make the website's packet appear at the router. It has to transmit data across the local network.

In a typical Wi-Fi setup, its networking software prepares the data for transmission, and the Wi-Fi hardware sends the encoded data over radio signals. The access point receives those signals and reconstructs the transmitted data.

The important point is that the laptop is transmitting to a nearby device over the local Wi-Fi network. It isn't directly transmitting radio signals to the remote website server.

If the router and Wi-Fi access point are separate physical devices, the radio transmission first reaches the access point, which then bridges the traffic toward the router. In many homes, both functions are combined into one box.

### 2. Why is a link-layer destination needed?

Let's separate two questions:

- Where is the data ultimately going? That's the purpose of the IP destination address.
    
- Which local device should receive this transmission? That's handled by the local network's delivery mechanism.
    

Ethernet and Wi-Fi each define rules for transferring data across a local link. These rules specify how data is framed for transmission and how the intended local recipient is identified.

That local delivery mechanism is what we mean by the link layer. The word layer is a way of grouping related responsibilities; it isn't necessarily a separate physical component.

For example, Wi-Fi provides rules for sending data from your laptop to a nearby access point over radio. Ethernet provides rules for sending frames across an Ethernet link.

These technologies need to identify the intended recipient of a local transmission. For Ethernet and Wi-Fi, MAC addresses are used in link-layer frame addressing.

### 3. Why isn't the IP address sufficient? Why does the laptop need the gateway's MAC address?

Because the IP destination and the local delivery destination serve different purposes.

Consider an ordinary postal delivery. A letter may ultimately be intended for someone in a distant city, but the first vehicle carrying it needs to know which local collection point should receive it next.

Likewise, the laptop knows the website's IP address, but the website is not its immediate local recipient. The laptop needs to hand its data to the gateway.

The IP packet and its local frame therefore have different addressing information:

Inside: IP packet

Source IP

192.168.1.20

Destination IP

203.0.113.40

Identifies the IP endpoints of this packet, subject to possible later address translation.

Outside: local link delivery

Source link address

Laptop's MAC

Next-hop link address

Gateway's local MAC

Identifies the participants in the local link transmission. Actual Wi-Fi framing has some additional details.

Why use MAC addresses rather than just putting IP addresses into Wi-Fi frames?

Because Ethernet and Wi-Fi define their own local framing and addressing rules. The IP packet is carried inside that link-layer structure. The network interface uses the link's rules to receive and transmit the frame; it doesn't replace those rules with IP routing.

This arrangement also allows IP to operate over different kinds of underlying networks. IP addresses identify network destinations, while the local link's addressing rules handle delivery over the current link.

### 4. How does the laptop find the gateway's MAC address?

The laptop already knows the gateway's IP address from its network configuration. But it may not yet know the corresponding local MAC address.

For IPv4, it can use ARP, the Address Resolution Protocol.

ARP is a local-network mechanism for asking which MAC address corresponds to a particular IPv4 address. If the laptop needs the gateway's MAC address and doesn't already have a usable entry in its cache, it broadcasts an ARP request.

The request effectively says:

“Which device has the IP address `192.168.1.1`? Tell me your MAC address.”

The gateway responds, and the laptop can cache the mapping for future use.

Why is it a broadcast? The laptop doesn't yet know the destination MAC address of the device it is asking about. Broadcasting the request lets devices on the local network receive the question; the device with the matching IP can reply.

This is a local-network procedure. The laptop doesn't ask the entire Internet for the gateway's MAC address.

## Have you understood the overall flow correctly?

Yes, with two qualifications.

Your summary was:

> The browser finds out the IP address of the server and now knows its own device's IP address and the IP address and the MAC address of the connected router. So, it now knows the destination address and the first step to reach that destination (sending packet to router).

That's essentially correct. More precisely:

1. The laptop obtains its network configuration, typically through DHCP. It learns its own IP address, subnet mask, default gateway address, and DNS resolver address.
    
2. The browser's networking components obtain the website's IP address, using DNS if a usable cached answer isn't already available.
    
3. The operating system determines the first hop. It uses its network configuration and routing information to determine whether the destination is local or needs to be reached through the default gateway.
    
4. The laptop obtains the necessary local link address, using ARP for IPv4 if it doesn't already have the gateway's MAC address cached.
    
5. The laptop can now prepare and transmit data toward that first hop. The IP destination identifies the website, while the local transmission is directed to the appropriate next device on the local network.
    

One small correction: it is not necessarily the browser itself that learns or stores all these addresses. The browser initiates the activity, but the operating system's networking components handle much of the network configuration, routing, and local address resolution.

Also, the laptop has not yet necessarily sent the HTTP request. It now has the information needed to begin the actual communication. For a new HTTPS-over-TCP connection, the transport connection and security setup typically happen before the browser sends its HTTP request.



Q: #### _**FROM THE REVISED SECTION 4**_

1. Why do different networks have different address configurations? I mean, what would happen if every device has a unique IP address?
2. So, when a device is connected to a router over Wi-Fi for the first time, it doesn’t get an internet connection immediately, correct?
3. How can the DHCP server identify the discovery message sending device? I mean, what is the identification it uses before assigning an IP address? It doesn’t have a name to call yet, right? How can the DHCP server understand that device ‘X’ is asking for a configuration?
4. How does the DHCP server choose a configuration? If the answer is a bit complex and not necessary for me to know, skip.
5. How does the device automatically choose from the offers?
6. Yes, not a requirement, rather, a stupidity. They should name them wisely, I think.
7. So, the IP address has meaning also, correct? Each part of it has separate meaning. But why do it need an extra number like subnet mask to identify local network and host? What do you mean by local subnet? Why can’t the IP address alone identify whether the destination belongs to the same network or not?
8. While transmitting data to a local network device, doesn’t the data packet travel to the router? Does it directly travel to the destination device?
9. So, DNS resolver address can the the IP address of the router, or of the ISP, correct?
10. I still haven’t understood why is it convenient for the link-layer to use MAC address instead of IP address? It could use source link IP and next-hop link IP. Then, why not? Look, if it used IP, it didn’t even have to go through ARP process.
11. You have described it all for Wi-Fi, what about mobile data or Ethernet?


A: ## 1. Why do different networks have different IP address configurations? What if every device had a unique IP address?

First, we need to distinguish an IP address being unique from an IP address indicating where a device belongs in the network.

Imagine three homes:

- Home A has a laptop with IP address `192.168.1.20`.
    
- Home B has a laptop with IP address `192.168.1.20`.
    
- Home C also has a laptop with IP address `192.168.1.20`.
    

How can this work? Because these are private addresses. Each home has its own separate network, so the same private address can be reused without ambiguity within those separate networks.

When the devices communicate with the Internet, their routers typically translate their private IPv4 addresses into public addresses using NAT.

But what if every device had a globally unique IP address?

That is possible, and IPv6 provides enough address space to support globally unique addresses on a vast scale. However, uniqueness alone doesn't solve everything.

Consider how routers find destinations. If every IP address were assigned randomly, routers could need enormous tables containing individual entries for vast numbers of devices.

Instead, IP addresses are assigned in a hierarchical way. Networks receive address ranges, and devices receive addresses from those ranges. A router can often use one routing entry for an entire range instead of keeping a separate entry for every destination.

For example, an ISP might advertise a prefix covering thousands of customer addresses. Other routers can forward traffic for that entire prefix toward the ISP.

This leads to an important conclusion:

- Uniqueness helps distinguish one address from another.
    
- Hierarchical allocation helps routers determine where addresses are reachable without maintaining a separate global entry for every device.
    

Different networks also have different configurations because they are independently administered. They may use different address ranges, gateways, subnet sizes, and DNS resolvers.

An IP address is therefore not necessarily a permanent identity for a physical device. It often reflects where the device is connected and how that network is configured.

## 2. Does a device get Internet access immediately after connecting to Wi-Fi?

Not necessarily. There are several steps, and connecting to Wi-Fi is only one of them.

Think of joining a network as entering a building. Getting inside the building does not automatically mean every service inside it is available.

For a typical laptop connecting to home Wi-Fi:

1. Wi-Fi association: the laptop establishes a wireless connection with the access point. If the network is secured, the necessary authentication and security setup must also succeed.
    
2. Network configuration: the laptop obtains a usable IP configuration, typically through DHCP, if it doesn't already have one.
    
3. External connectivity: the router must have a functioning connection to the ISP, and the laptop's traffic must be able to reach the Internet.
    
4. Destination discovery: the laptop needs to resolve a domain name or otherwise obtain the destination's IP address.
    

These operations can happen quickly and automatically, so it may seem as though Internet access begins immediately after connecting to Wi-Fi.

However, you can be connected to Wi-Fi while having no Internet access. For example, your laptop might successfully connect to the router even though the router's ISP connection is down.

## 3. How does the DHCP server identify a device that doesn't have an IP address yet?

It doesn't need the device to have a name such as `Nasif's laptop`, and it doesn't initially need the device to have a usable IP address.

Remember that communication involves more than IP addresses. The local network has its own mechanism for sending messages between nearby devices.

For example, on a typical Wi-Fi network, the wireless interface has a MAC address. During the initial DHCP exchange, the client can identify itself using information such as:

- Its link-layer source address.
    
- A client identifier included in the DHCP message, when provided.
    

DHCP messages also contain fields used to identify the client and associate messages with the configuration process.

When the laptop first asks for configuration, it can use an unspecified IPv4 source address because it hasn't obtained its normal IPv4 address yet. The request can be broadcast on the local network so that the DHCP server can receive it.

The server then uses the client's identification information and its own lease records to manage the request.

The important distinction is that identifying a client for network configuration is not the same as knowing the human or device's friendly name.

A MAC address doesn't tell the router that this is Nasif's laptop. It gives the network a link-layer address it can use to distinguish one interface from another in the relevant network context. A DHCP client identifier may also be used, and modern devices can use randomized MAC addresses.

## 4. How does the DHCP server choose an IP configuration?

The basic process is not especially complicated.

A DHCP server is configured with a range of addresses it is allowed to lease to devices on a particular network. This is often called an address pool.

For example, a home router might manage addresses in a range such as:

`192.168.1.100` through `192.168.1.200`

When a device asks for configuration, the server checks its configuration and lease records, then selects an appropriate available address. It also provides the relevant settings, such as the subnet mask, default gateway, DNS resolver, and lease duration.

The server needs to avoid assigning the same address to two clients simultaneously within that network. It manages leases and renewals to reduce this risk.

An administrator can also configure a reservation so that a particular client receives a preferred address.

You don't need to know the deeper selection algorithms yet. The essential idea is that the DHCP server has a defined address pool and configuration rules; it isn't inventing an arbitrary address every time a device connects.

## 5. How does the device automatically choose between multiple DHCP offers?

A client may receive offers from more than one DHCP server.

The DHCP protocol defines how the client communicates its selection. The exact offer-selection policy is an implementation decision, but the client normally chooses an offer it considers acceptable.

It then sends a DHCP Request identifying the selected server and the requested address. The selected server confirms the configuration with a DHCP Acknowledgment.

Other DHCP servers that receive the selection can recognize that their offers were not selected and make their offered addresses available again as appropriate.

So the laptop isn't necessarily comparing every offer and finding the mathematically best configuration. It follows a client-side selection policy and uses the protocol to communicate its choice.

## 6. Why are DHCP's message names so unintuitive?

Your criticism is understandable. The names are protocol terminology, not carefully designed wording for teaching beginners.

The important distinction is that the messages represent different actions in the process:

|Message|What it does|
|---|---|
|Discover|Searches for DHCP servers|
|Offer|Proposes configuration|
|Request|Communicates the client's selection or request for an address|
|Acknowledgment|Confirms the server's decision|

A DHCP Request isn't necessarily a direct linguistic reply to the preceding Offer. It's a particular message type that communicates the client's request or selection.

Once you understand their jobs, the names are less important than the state transition they represent.

## 7. What is a subnet mask, and why can't the IP address alone tell the laptop which network it belongs to?

An IP address has structure, but its network boundary isn't always self-evident from the address alone.

Consider:

`192.168.1.20`

Suppose its prefix is `/24`. That means the first 24 bits identify the network prefix, and the remaining 8 bits identify the address within that prefix.

The corresponding subnet mask is:

`255.255.255.0`

In this configuration, the local subnet is `192.168.1.0/24`, with addresses from `192.168.1.0` through `192.168.1.255` in the subnet's address range.

Now consider a `/16` prefix instead. Its subnet mask is `255.255.0.0`, and the network would cover a much larger range: `192.168.0.0/16`.

The numeric IP address is the same, but the network boundary differs.

That is why the laptop needs the prefix length or subnet mask as part of its network configuration. The IP address alone doesn't specify the prefix length in ordinary IPv4 configuration.

### What does “local subnet” mean?

It's the set of IP addresses that the device's configured prefix identifies as belonging to its directly connected IP network.

For example, if the laptop is configured as `192.168.1.20/24`:

- `192.168.1.35` is in its local subnet.
    
- `192.168.2.35` is outside its local subnet.
    

For the first destination, the laptop can attempt direct local delivery. For the second, it normally sends the packet to its default gateway.

This is how the subnet mask influences the laptop's forwarding decision.

The precise relationship between an IP subnet and the underlying local link depends on network configuration, but the `/24` example reflects the ordinary home-network setup we're discussing.

## 8. When sending data to a local device, does the packet go to the router first?

Usually, no—not through the router's IP-routing function, assuming both devices belong to the same ordinary local subnet.

Imagine this network:

- Laptop A: `192.168.1.20`
    
- Laptop B: `192.168.1.35`
    
- Router: `192.168.1.1`
    

If Laptop A wants to send data to Laptop B, it sees that Laptop B is in the same local subnet.

It can therefore address its local frame to Laptop B's MAC address instead of addressing the frame to the gateway.

The physical path depends on the connection technology:

- Ethernet: the frame commonly travels through an Ethernet switch to the destination device.
    
- Wi-Fi: the frame normally travels through the wireless access point, which forwards it across the local network.
    

If the access point, switch, and router are all inside the same physical box, the traffic may still pass through that box. But it need not be routed between IP networks.

This distinction matters: a router may be physically present on the path without its IP-routing function being used.

For a remote Internet destination, by contrast, the laptop addresses the local frame to the gateway. The gateway then forwards the IP packet toward the remote destination.

## 9. Can the DNS resolver's IP address belong to the router or the ISP?

Correct. Both are common possibilities.

The DNS resolver address provided to your laptop might be:

- Your home router's local IP address, such as `192.168.1.1`.
    
- An address belonging to your ISP's DNS resolver.
    
- An address belonging to a public DNS provider.
    

These options reflect different network configurations.

When the router is provided as the resolver, it may simply forward requests to another resolver rather than doing all the lookup work itself.

The laptop only needs a configured resolver address to know where to send its DNS query. It doesn't need to know every server the resolver might contact to obtain the answer.

## 10. Why use MAC addresses instead of IP addresses for local delivery? Couldn't the link layer use IP addresses and eliminate ARP?

This is a good question because the answer isn't that using MAC addresses is the only technically possible design.

A networking technology could be designed to use IP addresses directly for local delivery. But Ethernet and Wi-Fi use their own link-layer addressing because the link layer is designed to handle delivery over a local medium independently of the network-layer protocol.

There are three main reasons for this separation.

### Reason 1: Link-layer delivery should not depend on IP configuration

Ethernet and related link-layer technologies were designed to provide local communication mechanisms independently of a specific network-layer protocol.

The same link can carry IPv4, IPv6, and other types of network-layer payloads.

If Ethernet relied on IP addresses as its own addressing mechanism, its local-delivery mechanism would be more tightly coupled to IP. Devices and switches would need to use IP-specific information for tasks that belong to the local link.

With separate link-layer addresses, the Ethernet layer can perform its local-delivery job without needing the full IP networking system to be operational.

This also helps with initialization: a device can participate in local network operations, including the process of obtaining its IP configuration, before it has its usual IP address.

### Reason 2: Local forwarding can operate independently of IP routing

Imagine an Ethernet switch connecting twenty devices in an office.

The switch learns which MAC addresses are reachable through which physical ports. When a frame arrives, the switch can consult its MAC-address table and forward the frame through the appropriate port.

It doesn't need to perform an IP routing lookup for ordinary Layer 2 forwarding.

This separation is useful because a switch's job is to connect devices on a local network. A router's job is to forward IP packets between networks.

A switch can operate using link-layer information even when the payloads carried by those frames use different network-layer protocols.

A sufficiently sophisticated switch can inspect IP headers too, but that isn't required for its basic Layer 2 forwarding function.

### Reason 3: IP addresses describe network-level reachability, not just the local interface

An IP address is configured in the context of a network. A device can have different IP addresses on different interfaces or networks, and those addresses can change when the device moves between networks.

A MAC address, in contrast, is used for local link addressing. It is associated with a network interface in the relevant link-layer context, although it may be configured or randomized and isn't guaranteed to be a permanent hardware identity.

The local link can use its addressing system to deliver frames without requiring the IP address to serve both purposes.

### But doesn't this separation create unnecessary work through ARP?

It does introduce a resolution step for IPv4. When the laptop knows an IP address but doesn't have the corresponding local link address, ARP resolves that mapping.

There is a trade-off: a separate link-layer addressing system needs a way to connect network-layer destinations to local delivery addresses.

However, the laptop can cache the resulting mapping. It doesn't need to perform a new ARP exchange for every packet sent to the same local destination.

So the design trades a small amount of address-resolution complexity for a clearer separation between local delivery and network-layer addressing.

The key point: MAC addressing and IP addressing solve different problems. The Internet could have been designed differently, but the separation allows link technologies to provide local delivery independently of IP and other higher-layer protocols.

## 11. What changes with mobile data and Ethernet?

The general networking jobs remain, but the technology used to connect your device to the local network changes.

[Lynx LX-UTP5E-020-BLACK | Planeo](https://images.openai.com/static-rsc-4/M_1IqfgGw2pgDEbc6XNa_JDig3aw1rtgcv9_tG-ej_AQp47HzapJpYxRKip_DR-Ej7g0O7AKTgV7nRIEnTzr5kdZmKa8UYH3lAzZoy-ORkXc2tyRWgsm2L3iDfs-4CiZQngbVIQQlPOYRmGz9mBf4-cEloBpP8aSQvGKmCk_hMg?purpose=inline)

Ethernet

Your laptop's Ethernet interface sends and receives frames over a wired connection, usually through a switch or router.

The network commonly provides IP configuration through DHCP. The laptop then uses its routing table and local link addressing to send data toward the next hop.

[Computer Basics](https://images.openai.com/static-rsc-4/RSrD7XsYCCQwD0F7EkkTT341tm055_ZPt7mwEhC8TrLNGiKupuODpcRSi7-GZbUww6ktREAaIIXn084ZnNVbLxkwmCvkZhvdqshlxrtVCHaXwQpLXWOARuzqkN-JZq8q9VBP4lF-V2ROHrD8lMfkLDSimtoIurcT4DbHeg5h74o?purpose=inline)

Wi-Fi

The laptop transmits over radio to a Wi-Fi access point. In a typical home setup, DHCP provides the laptop's IP configuration, and the access point bridges local traffic toward the home network.

Wi-Fi uses its own link-layer framing and transmission rules rather than Ethernet's exact frame format.

[Was ist 5G? | Deutschland spricht über 5G](https://images.openai.com/static-rsc-4/PXV7UHotfrS3IWDqp9qMjFEJXFOhegWBGJu5B3YpqWWKmGXEGlo9ageesm-CoJhuW2cUSGocqGOhM8Zr9DLjGg3IFSKpTwlpxtaiYtdrm9jFf9pTzylJoG9-pGM55GumKP475JwPrqb3dDsnMIgmdXZKh-r23WhAHwOvp5pb-EU?purpose=inline)

Mobile data

Your phone connects through a cellular network using its modem and cellular protocol stack. The phone establishes service with the mobile operator, which provides the necessary packet-data connectivity and associated configuration.

This process is not simply the phone joining an ordinary Ethernet-like local network and obtaining its address through the same DHCP exchange used at home. The cellular network has its own control and data-transfer mechanisms.

The most important differences are in how the device connects to the access network and how that network provides connectivity.

On Ethernet and Wi-Fi, local link delivery commonly uses MAC-addressed frames, and DHCP commonly provides IPv4 configuration.

On mobile data, the phone communicates through cellular radio infrastructure and the operator's core network. The operator provides an IP data connection and relevant settings through cellular-network procedures.

After that, many of the higher-level jobs are still familiar. Your browser can use DNS to obtain a website's IP address, and the device's networking stack can route IP traffic through the active network interface.

The browser doesn't need to know whether the device is connected through Ethernet, Wi-Fi, or mobile data. The operating system and underlying network technology handle the differences.

### The complete picture so far

Your laptop needs three categories of information before it can send data to a remote website:

|Information|How it is obtained|Why it matters|
|---|---|---|
|Its own network configuration|Typically DHCP|Identifies the laptop in the network and tells it how to reach other networks|
|The website's IP address|DNS, unless already known or cached|Identifies the network destination|
|The next-hop link address|ARP for IPv4, if needed|Lets the laptop deliver its data to the first device on the local link|

The subnet mask helps the laptop decide whether a destination is local or remote. The default gateway provides a next hop for remote destinations. The MAC address identifies the local link recipient.

These are not redundant addresses. They provide different pieces of information needed to get data from your device to the correct destination.


Q: #### _FROM THE PREVIOUS SECTION 4 ANSWERS_

1. Still not clear. Why can’t unique IP addresses be designed in a hierarchical way too, it’s definitely possible? Why going through extra complexity of translation using NAT? What’s the benefit of it? I mean, whenever a digital device is built, can’t a unique IP address be assigned to it, like a globally unique ID number? And this can be hierarchical too like a phone number, each country has separate country code, each network operator within a country has unique prefix. I am confused about how Private IP solves hierarchy issue. Moreover, you said that networks receive address ranges, but from where?
2. Reasoning for why MAC address instead of IP address seems dubious to me. Because first of all you said that link layer delivery should not depend on IP configuration. Okay fine but you haven't really stated why. What's the issue if the local delivery mechanism is tightly coupled to IP? What's the problem with it? Same goes for the other two reasons. Let's let's explain an imaginary scenario where there is no MAC address, no subnet mask, only unique hierarchical IP address, which different part. Defines different information like a phone number. If we use it, what's the problem that arises? Then you tell me why MAC address and subnet mask both solves the problem. If not, the whole thing is sort of just complex and wrong. I can't blindly accept anything just engineers decided so.

You answer them and I am convinced then I will jump to section five and read it. Though I think it is not better written.



A: Your objections expose two weaknesses in my earlier explanation: I made NAT sound like a solution to the problem of hierarchical addressing, and I presented the separation between IP and MAC addressing as though it were strictly necessary.

Neither claim was accurate enough.

Your proposed design is technically possible. We could design a network in which every device has a globally unique, hierarchical IP address and local delivery uses that address directly. We need to examine what that design would gain, what information it would still need, and why the Internet's existing architecture makes different trade-offs.

## 1. Why not give every device a globally unique, hierarchical IP address?

Your telephone-number analogy is a good starting point.

Imagine a numbering scheme like this:

`Country → Network operator → Local network → Device`

Each part identifies a progressively smaller region of the addressing hierarchy. You could design a global addressing system on that principle.

In fact, IP addresses already use hierarchical allocation. The Internet's routing system assigns address ranges to networks, and those ranges can be represented as prefixes. Routers can then advertise routes to whole ranges instead of maintaining a separate route for every device.

RFC Editor+1

So you're correct about the central point: hierarchical addressing is possible. It is not the missing ingredient that NAT somehow provides.

There are actually three different questions tangled together here:

1. Can every device have a globally unique address?
    
2. Can those addresses be assigned hierarchically?
    
3. Should that address be a permanent identity of the physical device, or should it describe where the device is currently connected?
    

The first two are possible. The third is where important trade-offs appear.

### A. Why can't we assign an IP address when a device is manufactured?

We could assign a globally unique number to every device at manufacture. The problem is what that number tells the network.

Let's compare two designs.

### Design A: an address identifies the device permanently

Suppose a manufacturer gives a laptop a globally unique number derived from its manufacturer and serial number.

`Manufacturer ID + Device ID`

This identifies the device, but it doesn't tell Internet routers where the laptop is currently connected.

### Design B: an address identifies its network location

The address contains a prefix associated with the network and a remaining portion identifying an interface within that network.

`Network prefix + Subnet + Interface`

Routers can use the network prefix to send traffic toward the relevant network, then more specific information to deliver it within that network.

The first design is useful for identifying a device. The second is useful for routing traffic to its current network.

Suppose you take your laptop from your home in Chattogram to a network in Dhaka.

If its IP address contains a prefix identifying your home network, what happens when you move?

The Internet's routers will continue to interpret that prefix as pointing toward the home network. They won't magically know that the laptop has moved to Dhaka.

You have two choices:

- Keep the old address and introduce some mechanism that redirects traffic to the laptop's new location.
    
- Give the laptop an address appropriate for its new network.
    

The second is the basic approach used by ordinary IP addressing. The first is possible, but requires additional mechanisms for tracking location and forwarding data.

Could the address be globally unique and hierarchical? Absolutely. The catch is that when its hierarchy describes network location, moving networks may require changing the address.

Could you instead put a permanent device identifier and a changing network locator in the same address? You could, but routers would need to know how to use those different parts, and the design would need to handle the change in network location. Simply making the number unique doesn't solve that problem.

This is also why a device may have multiple IP addresses: it can have multiple network interfaces, belong to multiple networks, or use additional addresses for different purposes.

### So where do IP address ranges come from?

They are coordinated through the Internet's address-allocation system, rather than invented independently by each manufacturer.

At a simplified level:

IANA

Coordinates global IP address resources

Regional Internet Registries

Manage address allocations for their regions

ISPs and other network operators

Receive and allocate address blocks

Organizations and networks

Assign addresses and subnet prefixes to interfaces

This is a simplified hierarchy; actual allocation paths vary, and some organizations receive address allocations directly through a registry.

The Internet Assigned Numbers Authority (IANA) coordinates global address resources, including allocations to regional registries.

IANA+1

Notice something important: the hierarchy is built around networks and their address ranges, not simply around the manufacturers that produce the devices.

Why? Because a router needs to know which direction to forward traffic based on the destination's current network location. Allocating adjacent blocks to related networks makes it possible to summarize routes and reduce the size of routing tables.

RFC Editor+1

Your telephone-number analogy captures hierarchical allocation well. But IP routing has an additional requirement: the hierarchy must help the network find the destination, not merely tell us which company manufactured the device.

### Then why NAT? If addresses are hierarchical, what does NAT solve?

This is where my earlier explanation was misleading.

NAT does not solve the problem of hierarchical routing. It is primarily a way to make communication work between networks using non-global addresses and networks using globally unique addresses. IPv4 address scarcity was a major reason for its widespread adoption.

IPv4 addresses are 32 bits long, providing about 4.3 billion possible bit patterns before accounting for reserved and special-purpose ranges. That is insufficient to give every interface requiring Internet connectivity its own globally unique IPv4 address at today's scale.

Private IPv4 addresses solve a different problem: they let independently administered networks reuse the same address ranges internally.

For example, two homes can both use `192.168.1.20`. Those addresses aren't globally unique, but they are usable within their respective private networks. The private address ranges are reserved for this kind of use and aren't ordinarily routed across the public Internet.

RFC Editor+1

Here's where NAT comes in:

Your laptop

## 192.168.1.20

Private address

Home router performs NAT

Rewrites address and tracks the connection's translation

Internet

## Public IPv4 address

Shared by multiple devices in a typical home setup

For instance, when your laptop initiates a connection to a website, the router may replace the laptop's private source IP and source port with a public source IP and a different port. It records that mapping so incoming reply traffic can be translated back to your laptop.

This allows many devices to share a public IPv4 address.

RFC Editor+1

What does NAT buy us?

- Address conservation: a home with ten devices doesn't need ten public IPv4 addresses.
    
- Independent private addressing: an organization can manage its internal network without obtaining a separate globally unique address for every internal interface.
    
- Less dependence on the ISP's public address allocation: internal private addresses can often remain unchanged when the organization changes providers.
    

But NAT also has costs. It complicates unsolicited incoming connections, some applications, and end-to-end communication. It requires the translating device to maintain mappings and sometimes to inspect or modify transport information. NAT is not the same thing as a firewall, and it isn't a substitute for proper security controls.

RFC Editor+1

Your question remains: could we simply assign every device a globally unique, hierarchical address instead?

Yes. IPv6 was designed with enough address space to support globally unique addressing on an enormous scale, and its global unicast architecture explicitly supports hierarchical prefixes.

RFC Editor+1

With IPv6, a network can generally assign globally unique addresses to its interfaces without translating them into one shared public IPv4 address. Firewalls and other security controls can still restrict incoming traffic.

So the correct conclusion is not that private IP addresses are needed to make hierarchical routing possible. They aren't.

The distinction is:

- Hierarchical addressing makes routing scalable.
    
- Private addressing permits address reuse in separate networks.
    
- NAT translates between address realms, commonly to conserve public IPv4 addresses.
    

They are three different concepts.

## 2. Imagine an Internet with no MAC addresses and no subnet masks

Let's take your proposal seriously and design the alternative from scratch.

We'll assume the following:

- Every device has a globally unique IP address.
    
- Its address contains hierarchical information.
    
- The local delivery system uses IP addresses directly instead of MAC addresses.
    
- There is no separately configured subnet mask.
    

Could such a network work?

Yes, in principle. We need to design the details properly, but there is no fundamental law that says a network must use MAC addresses and subnet masks in precisely the way Ethernet and IP do today.

Let's examine the design and identify the trade-offs.

### 2.1 First, design your hierarchical IP address

Imagine every IP address has these fields:

Global routing prefix

Subnet

Interface ID

Find the network

Find its subnet

Find the interface

This is not an entirely imaginary structure. IPv6 global unicast addresses use a global routing prefix, a subnet identifier, and an interface identifier in their addressing architecture. The exact division depends on the assigned prefix and subnetting plan.

RFC Editor+1

Now imagine that your laptop's address is:

`[network prefix A][subnet 5][interface 20]`

And a website's address is:

`[network prefix B][subnet 3][interface 40]`

The address hierarchy lets routers direct the packet toward network prefix B. Further routers can use more specific routes to reach the destination network.

This part of your proposed design is sound. The Internet already works with the same basic idea.

But we still have two questions:

1. How does the laptop know whether another device is local or remote?
    
2. How does the local network physically deliver a transmission to the intended receiving device?
    

Let's solve each without assuming the existing MAC-address system.

### 2.2 Can we eliminate the subnet mask?

Yes, provided we replace it with an equally clear way to determine the network boundary.

A subnet mask is not a magical ingredient required for IP addresses to exist. It is one way to specify which bits of an address identify a particular network prefix.

For example, `192.168.1.20/24` means that the first 24 bits belong to the prefix. The `/24` tells the computer where the boundary is.

Your alternative could instead define addresses with fixed field sizes. For instance, it could specify that the first 64 bits always represent the network hierarchy and the remaining 64 bits represent the local interface.

Under that design, the device would not need to receive a separate subnet mask to discover that fixed boundary.

But this introduces a trade-off: different networks need different sizes. A tiny network doesn't necessarily need the same address allocation as a huge network. If every boundary is fixed, address allocation becomes less flexible, and variable-sized address ranges can no longer be represented as freely.

Alternatively, you could explicitly encode the prefix length in the address format or provide it through network configuration. In the latter case, you've effectively retained the information supplied by a subnet mask, even if you call it something else.

This is the central point: the network needs a way to identify the prefix boundary, but that information does not have to be stored in a separately named subnet mask.

The current Internet uses variable-length prefixes. For IPv4, subnet masks or prefix lengths provide the boundary information. For IPv6, prefix lengths such as `/64` serve the same conceptual purpose. Routers also need prefix lengths in their routing tables so they can choose the most specific matching route.

RFC Editor+1

### 2.3 Can we eliminate MAC addresses and use IP addresses for local delivery?

Now let's redesign Ethernet or Wi-Fi's local delivery system.

In the current system, an IP packet carries the ultimate IP destination, while the local frame identifies the receiver of that particular transmission.

Suppose your laptop wants to reach a website through its home router.

- The IP packet's destination is the website.
    
- The local transmission needs to reach the router.
    

Your proposal could define the local frame's destination field as an IP address rather than a MAC address. Your laptop would put the gateway's IP address in that field, while the IP packet inside would still identify the website as its final destination.

The switch or wireless access point would then have to understand and use these IP-address-based local destinations to forward the transmission.

Could this work? Yes. But it would make the local delivery technology depend on IP addressing and require the relevant network equipment to implement this alternative.

Let's compare the two designs:

### Existing Ethernet/Wi-Fi-style approach

IP packet

Destination: Website's IP

Local frame

Destination: Gateway's MAC address

The local link uses its own addressing rules. ARP can translate the gateway's IPv4 address into the MAC address required by Ethernet.

### Your proposed alternative

IP packet

Destination: Website's IP

Local frame

Destination: Gateway's IP address

The local link must support IP-address-based delivery, and its switches or access points must understand that addressing scheme.

Your alternative can avoid the specific ARP lookup used by Ethernet because the local transmission addresses the gateway using its IP address.

But notice what you've done: you've changed the local network's framing and forwarding design. You've not eliminated the need to distinguish the final IP destination from the next local receiver. You've chosen to represent both with IP addresses.

You would also need an initialization mechanism. When a device first connects and has not yet obtained its normal IP address, it must still be able to participate in network configuration. Your new protocol would need a way to broadcast or otherwise deliver configuration messages before ordinary IP-based local delivery is available.

That problem is solvable, but it must be addressed.

### 2.4 What actual problems does MAC addressing avoid?

Now let's be precise. Using IP addresses at the link layer isn't inherently wrong. The existing architecture simply has different advantages.

First, the link layer can operate independently of the IP protocol.

Ethernet defines a frame format with MAC addresses and a field identifying what type of data the frame carries. IP is one possible payload. The same Ethernet framework historically carried other network protocols as well.

RFC Editor+1

If the local delivery mechanism required IP addresses, it would be more tightly coupled to IP. A device would need the IP-oriented addressing system to participate in that delivery mechanism. Supporting other network protocols would require additional design decisions.

That's not an impossible problem. It is a loss of independence between the two systems.

Second, link-layer forwarding can be simpler when it is confined to local delivery.

A conventional Ethernet switch can learn MAC addresses and forward frames without implementing the entire IP routing system.

If you change the local addressing scheme to use IP addresses, the equipment must learn and forward using those addresses or some mapping derived from them. You could still build a simple switch, but you'd have designed a different forwarding system.

The issue is not that IP-aware switches are impossible. Modern network equipment often examines IP information. The point is that the existing separation allows ordinary link forwarding to work without requiring full IP-routing behaviour.

Third, local addressing and network addressing have different lifetimes and scopes.

Your device's IP address can change when it joins a different network. Its network interface can have multiple IP addresses.

A MAC address provides the local link's addressing identifier independently of which IP address is currently configured. It can remain usable for local network communication while the device obtains or changes its IP configuration.

This doesn't make MAC addresses perfect permanent identities. MAC addresses can be changed or randomized, and their uniqueness guarantees apply within the relevant link-layer context. Their purpose is local delivery, not permanent global identification.

### 2.5 Is ARP an unnecessary complication, then?

It is a complication, but it's a consequence of the specific design used by Ethernet and IPv4.

Ethernet requires a destination MAC address in its frame. The laptop has an IP address for its gateway but may not yet have that MAC address. ARP bridges the gap between the two addressing systems.

RFC Editor+1

Your alternative could remove that particular translation by designing the link to use IP addresses directly.

But the right comparison isn't:

- Current design: needs ARP.
    
- Alternative: doesn't need ARP, therefore must be better.
    

It is:

- Current design: separate link-layer addressing, with ARP to map IPv4 destinations to local link addresses.
    
- Alternative: IP-based local addressing, requiring a different framing and forwarding design, plus a way to handle initial configuration and local delivery.
    

Either architecture is possible. They have different costs and advantages.

There are also existing network types that don't use Ethernet-style MAC addressing in the same way. Point-to-point links, for example, can carry IP without requiring the familiar Ethernet ARP procedure. So MAC addressing is not a universal requirement for transmitting IP packets.

## 3. Why do we need both a subnet mask and a MAC address? Don't they solve the same problem?

This is the final distinction to make.

They solve two different problems at two different stages. Neither substitutes for the other.

Let's follow one concrete transmission.

Your laptop has:

- IP address: `192.168.1.20/24`
    
- Gateway IP: `192.168.1.1`
    
- Gateway MAC: `AA:AA:AA:AA:AA:AA` (illustrative)
    
- Destination server IP: `203.0.113.40` (illustrative)
    

The laptop wants to send a packet to the server.

First, it needs to determine the next hop.

It uses the local IP prefix, `/24`, to determine that the server is not on its directly connected subnet. It therefore selects the gateway as the next hop.

The prefix information answers:

Should I attempt direct local delivery, or should I forward this packet through a gateway?

Second, it needs to transmit to that next hop.

The laptop knows the gateway's IP address, but Ethernet requires a destination MAC address for its outgoing frame. If the address is not cached, ARP discovers the gateway's MAC.

The MAC address answers:

Which device should receive this frame on the local link?

The laptop then transmits a frame addressed to the gateway's MAC, carrying an IP packet whose destination is still the server's IP address.

That is the entire relationship.

|Mechanism|Question answered|
|---|---|
|IP destination address|Where is the packet ultimately going?|
|Subnet mask or prefix length|Is the destination on the local subnet, and which network prefix applies?|
|Routing table and default gateway|Which next hop should receive traffic for that destination?|
|MAC address|Which local link interface should receive this frame?|
|ARP|What MAC address corresponds to a particular local IPv4 address?|

The subnet mask does not identify the local physical recipient. The MAC address does not tell the laptop whether the ultimate destination is in another IP network. The gateway and routing table provide the forwarding decision, and ARP supplies the local address mapping required by Ethernet.

## 4. The honest conclusion

You were right to challenge the earlier reasoning. I made a design choice sound like a fundamental necessity.

Your proposed architecture—globally unique hierarchical addresses, with network boundaries defined by the address format and IP addresses used for local delivery—is technically possible. Its biggest difference is that the local networking technology would need to be designed around those choices.

The existing Internet separates network-layer addressing from link-layer addressing partly because it allows IP to operate over different local networking technologies, keeps local framing responsibilities independent, and supports forwarding without requiring every link-layer device to implement the same functions as an IP router.

Likewise, a subnet mask is one way of expressing a network-prefix boundary. The boundary information is necessary for the routing decisions we're discussing, but it doesn't have to be represented by a separately named mask in every possible architecture.

And NAT is largely a workaround for the limited public IPv4 address space and the use of private address ranges—not a solution to the problem of hierarchical addressing.

The distinction to retain is that an architecture should be justified by the problems it solves and the costs it introduces, not accepted merely because it became standard.