---
type: concept
module: 2
unit: 1
order: 1
written_by: claude
tags:
  - medium
source: "[[kurose-ch01]]"
---
# 1. The Internet as a system

*Where we are: before following one request from your laptop to `bhara.com` and back, we need to know who is in the story and what rules they share.*

## The problem

Billions of devices want to talk to each other. They are built by different companies, run different operating systems, and are connected through networks owned by thousands of different organisations, none of which is in charge of the whole thing. A phone in Chattogram, a laptop in a café in Dhaka and a server in Singapore have never met and have no shared owner. Yet a message from one reaches the other in a fraction of a second.

So the first question is not "how does a packet travel?" but "what is this thing, and how can parts that were never designed together cooperate at all?"

There are two useful ways to look at it. One looks at the hardware (what is physically there). The other looks at the service (what it does for programs). Both are true at the same time.

## The nuts-and-bolts view: what is physically there

**Hosts, or end systems.** These are the devices at the edges that run programs: your laptop, your phone, a smart TV, a server in a data centre. They are called *end* systems because they sit at the ends of every conversation. Everything in between only carries data for them.

A host plays one of two roles in a given conversation. The **client** asks; the **server** answers. These are roles, not kinds of machines. Your laptop is a client when it fetches a web page and a server if you run a development server on it and your phone connects to it.

**Communication links** connect the devices: copper cable, optical fibre, radio (Wi-Fi, mobile data), even satellite. Each link has a *transmission rate*, measured in bits per second, which is how fast a sender can push bits onto it.

**Packet switches** sit between links and pass data along. There are two main kinds:

- **Routers** connect different networks and decide where data goes next based on its final destination.
- **Link-layer switches** connect devices *within* one local network.

A home Wi-Fi "router" is usually several of these in one box. Note 5 and note 7 come back to that.

**Packets.** A host does not send a file as one long stream down a wire. It cuts the data into pieces, attaches a small block of information (a *header*) to each piece, and sends the pieces separately. Each piece with its header is a **packet**. The destination puts the pieces back together. Note 2 explains why the Internet works this way.

**Route, or path.** The sequence of links and packet switches that a packet passes through on its way from the sending host to the receiving host.

**ISPs (Internet Service Providers).** Nobody owns "the Internet". It is a **network of networks**. Your home connects to an access ISP (your broadband or mobile operator). Access ISPs connect to larger regional ISPs, which connect to a small number of very large global networks (often called tier-1). Networks also meet directly at exchange points to swap traffic, and big content companies such as Google run their own global networks. Each of these networks is run independently, which is why the Internet needs agreed rules to work at all.

**IoT (Internet of Things)** is simply the growing set of non-traditional devices that are also hosts: cameras, thermostats, sensors, cars.

## The service view: what it does for programs

From a program's point of view, the Internet is infrastructure that delivers data between programs running on different hosts. A browser on your laptop and a web server on another machine are two halves of one *distributed application*. Neither cares how many cables and routers are in between.

To use that service, a program must follow some rules for handing data to the network. Kurose calls these rules the **socket interface**: "a set of rules that the sending program must follow so that the Internet can deliver the data to the destination program." Note 4 explains sockets properly. For now, think of a socket as the doorway between a program and the operating system's networking code.

## Protocols: how strangers cooperate

Because no single company built the whole system, every interaction has to follow published, shared rules. Those rules are **protocols**.

Kurose's definition is precise, and every word matters:

> A protocol defines the **format** and the **order** of messages exchanged between two or more communicating entities, as well as the **actions** taken on the transmission and/or receipt of a message or other event.

So a protocol has three parts:

1. **Format.** What a message looks like: which fields, in which order, how big, what each value means.
2. **Order.** Which message may come first, and which message is allowed to follow which.
3. **Actions.** What each side must *do* when it sends or receives a particular message, or when something happens, such as a timer running out.

A human example makes it concrete. You walk up to someone and say "Hi." They reply "Hi." You ask "Do you know the time?" They say "2:00." There is a format (greetings, a question, an answer), an order (greeting before question, question before answer), and actions (if they ignore your "Hi", you don't ask the question; you walk away). Break any one of these and the exchange fails.

> [!question] You asked (Gemini)
> "I am having difficulty to understand the connection among HTTP, TCP, IP. I am not understanding what these protocols truly mean and what is the format of their protocols (protocol is a standardized format to send data or vice versa, if I am right) and the order of their happenings or operations."

That guess, "a protocol is a standardized format", is the most common half-definition, and it is worth fixing early. Format is only one of the three parts. A protocol that defines only the format is a file format, not a conversation.

> [!question] Then you pushed back (Gemini)
> "You told me that a protocol defines 3 things: message format, order, action. In HTTP, yes, I understood the format and action, but what about the order? Same goes for IP."

This was a good test of the definition against real examples. Here is the honest answer.

- **HTTP's order** is strict: the client must send a request first, and the server may send exactly one response to it. A server cannot start talking to a browser that has not asked for anything. Request, then response, always in that order.
- **IP's order** is almost empty, and that is itself a design decision. IP handles each datagram on its own: one message, no exchange, no promise about which of two datagrams arrives first. Most of IP's "protocol" is format (the header) and actions (for example, a router must lower a counter in the header and must throw the datagram away if the counter reaches zero; note 9 explains why). Ordering between datagrams is deliberately left to the protocol above it, TCP (note 11).

You will see the "order" part most clearly in DHCP (note 5), whose four messages must happen in a fixed sequence, and in TCP's handshake (note 11).

## Standards: why different vendors can cooperate

Protocols only work if everyone implements them the same way, so they are written down publicly:

- The **IETF** publishes Internet protocols (IP, TCP, UDP, DNS, HTTP, TLS) as documents called **RFCs** ("Request for Comments"). Despite the name, many RFCs are binding standards.
- The **IEEE 802** committees standardise the link technologies: 802.3 is Ethernet, 802.11 is Wi-Fi.

This is why a Samsung phone, a Cisco router and a Linux server in a data centre can talk without their makers ever coordinating. They all follow the same documents.

## Slips to avoid

- **"Protocol = format."** It is format *and* order *and* actions.
- **"Client and server are types of computer."** They are roles in one conversation.
- **"The Internet is the Web."** The Web (HTTP and web pages) is one application that runs *over* the Internet. Email, DNS, video calls and games are others.

## Check yourself

1. Name the three things a protocol defines, and give an example of each from human conversation.
2. Why does the Internet need published protocols at all? What would break without them?
3. Your laptop runs a development server that your phone connects to. Which device is the client and which is the server?
4. Give one protocol whose "order" part is strict and one whose "order" part is almost empty.

> [!success]- Answers
> 1. Format (what a message looks like), order (which message may follow which), actions (what each side does on sending, receiving or other events, such as a timeout). Example: greeting format; greeting before question; walking away if ignored.
> 2. The Internet is built from independently owned networks and devices made by many vendors. Without published rules, no two independently built parts could interpret each other's messages.
> 3. The phone is the client (it asks), the laptop is the server (it answers). Roles, not machine types.
> 4. Strict: HTTP (request before response), DHCP (four messages in sequence), TCP's handshake. Nearly empty: IP (each datagram handled on its own, no ordering promise).

---
[[m02-internet|Module 2]] · Next: [[m02-02-packet-switching-and-delay]] →
