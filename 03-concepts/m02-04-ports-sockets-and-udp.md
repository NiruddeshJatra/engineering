---
type: concept
module: 2
unit: 2
order: 4
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 4. Ports, sockets and UDP

*Where we are: we have the layer model. Before following the journey, one more building block that everything after it depends on: how data reaches the right program, not just the right machine.*

## The problem: a machine is not a program

You framed this yourself, and it's the right starting point:

> [!question] You asked (Gemini)
> "I mean, let's say IP address and port number is needed to identify the host and the process running within the host. Then what? What more is needed?"

An IP address gets data to a *machine*. But a machine runs many programs at once: a browser with twelve tabs, WhatsApp, a music app syncing in the background, maybe a database if it's a server. A web server machine may also run an SSH server for administrators and a mail server. When a datagram arrives, the operating system has to decide which of those programs gets it.

The IP header can't answer that. It only names machines. Something one layer up has to name programs.

## Ports: numbers for programs

A **port** is a 16-bit number (0 to 65,535) that identifies an endpoint for communication on a machine. Every transport-layer segment carries a **source port** and a **destination port** in its header.

Think of a large office block. The IP address is the building's street address. The port is the extension number or office number inside. The postal service (IP) only delivers to the building; the building's mailroom (the operating system) uses the office number to deliver inside.

**Servers listen on well-known ports**, so clients know where to knock without being told:

| Port | Service |
|---|---|
| 53 | DNS |
| 67 / 68 | DHCP (server / client) |
| 80 | HTTP |
| 443 | HTTPS |
| 22 | SSH |
| 5432 | PostgreSQL |

When you type `https://bhara.com`, the browser knows to connect to port 443 because that's the convention for HTTPS. A URL like `http://localhost:8000` names the port explicitly.

**Clients use temporary ports.** When your browser opens a connection, the operating system picks an unused high-numbered port for your side, called an **ephemeral port** (for example 52314). It's chosen per *connection*, not per tab or per program. One tab may open several connections, and each gets its own source port.

> [!question] You asked (Gemini)
> "How does the part of the computer or service which sets up TCP Segment know which the port should be?"

The destination port comes from the application: the browser knows HTTPS means 443, or the URL says otherwise. The source port is chosen by the operating system when the program opens the connection. The program never invents its own source port unless it deliberately asks for one.

## Sockets: the doorway between program and OS

Programs don't build TCP or IP headers themselves. In your laptop, TCP and IP live inside the operating system's [[Kernel|kernel]] (the privileged core of the [[Operating System|operating system]]). A program in user space can't reach into the kernel's networking code directly. It needs a doorway.

That doorway is a **socket**: an interface the operating system gives a program for sending and receiving network data. The program asks the OS for a socket, tells it where to connect (an IP address and port), and then simply writes bytes into it and reads bytes out of it. Everything below that is the operating system's job.

Kurose's phrase from your notes, the "Internet socket interface: a set of rules that the sending program must follow so that the Internet can deliver the data to the destination program," is exactly this.

A socket is not a cable, not a layer, and not a port. It's the program's handle on one communication endpoint. In Unit 4 you'll create sockets yourself with Python's `socket` module.

### How the OS finds the right socket

When a segment arrives, the operating system looks at its headers and hands the data to the matching socket. This step is called **demultiplexing**; the opposite, gathering data from many sockets into outgoing segments, is **multiplexing**.

- For **UDP**, a socket is identified by its local IP address and port. Everything arriving at that port goes to that socket.
- For **TCP**, a *connection* is identified by four values: source IP, source port, destination IP, destination port.

Why four values for TCP? Because a busy server has thousands of clients connected to the *same* port 443 at the same time. The destination port alone can't tell them apart. The pair (client IP, client port) does. Each accepted connection gets its own socket on the server.

## UDP: the bare minimum

> [!question] You complained (ChatGPT)
> "You mentioned UDP without explaining it, bad experience."

Fair. Here it is, before anything uses it.

**UDP (User Datagram Protocol)** is the simplest possible transport protocol. It adds exactly what the transport layer must add, and nothing more. Its header is 8 bytes:

| Field | Purpose |
|---|---|
| Source port | Who to reply to |
| Destination port | Which program gets it |
| Length | Size of header plus data |
| Checksum | Detect corrupted data |

What UDP does **not** do:

- No connection setup: the first packet already carries data.
- No acknowledgments: the sender never learns whether it arrived.
- No retransmission: a lost datagram stays lost.
- No ordering: datagrams may arrive in any order.
- No speed control: it sends as fast as the program asks.

So why does it exist? Because for some jobs, those missing features cost more than they're worth:

- **Tiny question, tiny answer.** A DNS lookup (note 10) is one small question and one small answer. Setting up a connection first would double the time. If the answer doesn't come, the program just asks again.
- **Before you have a proper address.** DHCP (next note) runs before your device has an IP address of its own. A connection-based protocol would be awkward there.
- **When late is useless.** In a voice call or online game, a packet that arrives half a second late is worthless. Retransmitting it just delays everything behind it. Better to skip it and play the next one.
- **As a base to build on.** UDP passes through almost every network unchanged. QUIC, the transport under HTTP/3 (note 13), builds its own reliability on top of UDP for exactly this reason.

The reliable alternative, TCP, needs more machinery: connections, sequence numbers, acknowledgments. It has its own note (note 11), after we've seen what it's protecting against.

## Slips to avoid

- **"The OS gives each browser tab a port."** Ports are chosen per connection.
- **"Socket = port."** A port is a number in a header. A socket is a program's handle on an endpoint.
- **"UDP is unreliable, so it's bad."** It's minimal by design. Reliability is added where it's worth the cost.
- **"A server's port identifies the connection."** For TCP, only the four values together do.

## Try it (Windows)

```
netstat -an | findstr ESTABLISHED
```

Each line is one TCP connection: local address:port, remote address:port. Open a website and run it again: new lines appear with high local port numbers (your ephemeral ports) and remote port 443.

## Check yourself

1. What does an IP address identify, and what does a port identify?
2. Who chooses the source port of a browser's connection, and who chooses the destination port?
3. Why must a TCP server use four values, not just its port, to find the right connection?
4. Name the four fields of the UDP header and three things UDP deliberately does not do.
5. Give two situations where UDP is the better choice, and why.

> [!success]- Answers
> 1. IP: a machine (strictly, a network interface). Port: an endpoint, usually one program's socket, on that machine.
> 2. Source: the operating system (ephemeral port). Destination: the application, from convention (443 for HTTPS) or the URL.
> 3. Thousands of clients connect to the same server port at once; only client IP plus client port distinguishes them.
> 4. Source port, destination port, length, checksum. No connection setup, no acknowledgments or retransmission, no ordering (and no rate control).
> 5. DNS (one small question and answer; a handshake would double the time). Voice and games (late data is useless; retransmission only adds delay). Also DHCP (runs before the device has an address) and QUIC (builds its own reliability on top).

---
← [[m02-03-layers-and-encapsulation]] · [[m02-internet|Module 2]] · Next: [[m02-05-joining-a-network-dhcp]] →
