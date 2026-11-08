---
type: concept
module: 2
unit: 1
order: 8
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 8. Public and private addresses, NAT, and who hands out addresses

*Where we are: the frame has reached your home router. Before the datagram leaves your home, the router changes something in it. To see why, we need to know why your laptop's address, `192.168.1.20`, means nothing outside your house.*

## The problem, in your own example

> [!question] You asked (Gemini)
> "… let's say my IP address is 192.168.0.2 and my father's is 192.168.0.3. My mother's IP address, the private IP address in another network, is 192.168.0.2.
> 1. You said that this packet will be dropped by my router but how will it know that this address is not in our own network? How will it identify that?
> 2. … how can I actually send a message to my mother? Why are there two types of IP addresses? Just keeping the public IP addresses for all the devices, isn't it simple?"

Walk through what your phone actually does if you try to send to `192.168.0.2`:

- Your phone applies its subnet mask (note 6). `192.168.0.2` is on your own subnet, so it's "local." It never even goes to the router.
- In your example it's worse: `192.168.0.2` is *your own* address. Your phone delivers the data to itself without it ever leaving the device.
- If your mother's address were, say, `192.168.0.9`, your phone would ARP for it on your home Wi-Fi (note 7). Nobody in your house has that address, so nobody answers, or the wrong device does.

Either way, the data never leaves your home. And even if it did, the routers of the Internet would have nowhere to send it. Addresses like `192.168.x.x` are **private**: every home in the world reuses them, so no router on the public Internet has a route for them. They're meaningful only inside one local network.

So how *do* you reach your mother? Not by her address at all. Both your phones keep a connection open *outward* to WhatsApp's servers on the public Internet. You send your message to the server; the server passes it down her phone's existing connection. The server is the meeting point, because both of you can reach it but you can't reach each other.

## Why private addresses exist: IPv4 ran out

IPv4 addresses are 32 bits, which gives about 4.3 billion possible addresses, and fewer than that are usable once special ranges are set aside. Today there are many more devices than that: phones, laptops, TVs, cameras, servers. If every device needed its own public address, the Internet would have run out long ago. In fact it did: the central pool of unassigned IPv4 addresses ran out in 2011, and the registry for Asia–Pacific (APNIC, which covers Bangladesh) reached its final block the same year.

The workaround has two parts.

**1. Private address ranges.** Three blocks were set aside (in a standard called RFC 1918) for anyone to use internally:

| Range | Size |
|---|---|
| `10.0.0.0/8` | about 16.7 million addresses |
| `172.16.0.0/12` | about 1 million |
| `192.168.0.0/16` | about 65,000 |

Millions of homes all use `192.168.1.x` at the same time without conflict, because these addresses are never used on the public Internet.

**2. NAT** (*Network Address Translation*), which lets all the private devices behind one router share a single public address when they talk to the outside world.

## How NAT works

> [!question] You asked (Gemini)
> "What do you mean by single shared IP address? An shared IP address of router+device?"

The public address belongs to your router's *ISP-side* interface. To every server on the Internet, your entire household looks like one machine with that one address.

When your laptop sends a datagram out, the router rewrites its source:

| | Source | Destination |
|---|---|---|
| Leaving your laptop | `192.168.1.20 : 52314` | `bhara.com's IP : 443` |
| Leaving your router | `203.0.113.7 : 40001` | `bhara.com's IP : 443` |

The router records the mapping in a **translation table**: "outside port 40001 ↔ `192.168.1.20 : 52314`." When the reply comes back addressed to `203.0.113.7 : 40001`, the router looks up port 40001, rewrites the destination back to `192.168.1.20 : 52314`, and passes it into the house.

Notice what makes this possible: **ports** (note 4). The router tells dozens of devices' connections apart by giving each one a different outside port. Strictly this is called NAPT (*network address and port translation*), but everyone just says NAT.

### What NAT costs

- **Nobody outside can start a conversation with you.** A datagram arriving from outside with no matching table entry has nowhere to go. That's why apps use servers as meeting points, and why video calls use extra techniques: one helps each phone discover its own public address and port, and when a direct path still fails, the call is relayed through a server.
- **It breaks the layering.** A layer-3 device rewrites layer-4 port numbers, and has to fix the TCP and UDP checksums as it goes (note 3).
- **NAT is not a security feature by design.** Blocking unsolicited incoming traffic is a side effect. A firewall is a separate thing.

## Your ISP probably does it too: carrier-grade NAT

The ISPs ran short of IPv4 addresses as well. Many of them, especially in fast-growing markets, don't give each home its own public address. Instead they put many customers behind *one* public address using NAT inside the ISP's network. This is **carrier-grade NAT (CGNAT)**. Your traffic is translated twice: once by your router, once by the ISP.

Your own traceroute (note 9) suggests your ISP does this. Hop 2, the first router after your home router, is `10.8.66.1`, which is a private address. If your router's ISP-side address were public, the next router would normally be public too.

You can check:

1. Open your router's admin page (usually `http://192.168.1.1`) and find its **WAN** or **Internet** IP address.
2. Search "what is my IP" in a browser.
3. If the two addresses are **different**, you're behind CGNAT. The address websites see belongs to your ISP and is shared with other customers. (A WAN address in `100.64.x.x` to `100.127.x.x` is a giveaway: that range was reserved specifically for CGNAT.)

Consequence: you can't host a server from home or open a port on your router and expect the world to reach it.

## Who hands out public addresses

> [!question] You asked (Gemini)
> "Ok, so routers have public IP addresses. Who assigns them? I mean, home device's private IP addresses are assigned by the router generally, who assigns the public address for the routers?"

A chain of delegation, from the top down:

1. **IANA** (Internet Assigned Numbers Authority) coordinates the global address space and gives large blocks to five regional registries.
2. **Regional Internet Registries (RIRs):** AFRINIC (Africa), APNIC (Asia–Pacific, including Bangladesh), ARIN (North America), LACNIC (Latin America), RIPE NCC (Europe and the Middle East).
3. **ISPs and large organisations** become members of their RIR and receive address blocks.
4. **Customers** get an address from their ISP's block, either fixed (static, often at extra cost) or changing (dynamic), or, under CGNAT, a private one.

So your home router gets its ISP-side address from your ISP the same way your laptop got its address from your router: by being told, when it connects.

## Why not one unique address per device, like a phone number?

This was your deepest question on addressing:

> [!question] You asked (ChatGPT)
> "Why can't unique IP addresses be designed in a hierarchical way too, it's definitely possible? Why going through extra complexity of translation using NAT? What's the benefit of it? I mean, whenever a digital device is built, can't a unique IP address be assigned to it, like a globally unique ID number? And this can be hierarchical too like a phone number, each country has separate country code, each network operator within a country has unique prefix. I am confused about how Private IP solves hierarchy issue. Moreover, you said that networks receive address ranges, but from where?"

There are three separate ideas tangled together here, and pulling them apart answers the question.

**1. Hierarchy.** IP addresses already *are* hierarchical: registry → ISP → customer → subnet → device. That's what lets a router use one entry for a whole ISP's block (note 9). You're right that this is possible and good. It is not what NAT provides.

**2. Identity versus location.** This is the real catch, and your phone example shows it perfectly once you look at a phone closely. A mobile phone has **two** numbers:

- The **IMEI**, set at the factory, identifying the handset. That's like a MAC address: unique, flat, and it says nothing about where the phone is.
- The **phone number**, given to you by the operator when you subscribe. That's like an IP address: it's hierarchical (country code, operator prefix) *because it's assigned by the network you join*.

A factory can't put a hierarchical address into a device, because the hierarchy describes the network, and the factory doesn't know which network the device will join. That's exactly why your laptop gets its address from DHCP when it connects (note 5).

And what happens when a phone *moves* but keeps its number? When you travel abroad, the phone network keeps a database of where every subscriber currently is and redirects your calls there. When you switch operators and keep your number, the country maintains a lookup database to find out which operator now owns it. Keeping an address fixed while its owner moves requires a location-tracking system. IP made the opposite choice: **when you move, your address changes**, so routers never have to track individual devices. If you take your laptop from Chattogram to Dhaka, it gets a new address there.

**3. Scarcity.** Private addresses and NAT exist because 32 bits weren't enough for every device. They don't help hierarchy at all. They're a workaround for running out.

| Idea | Problem it solves |
|---|---|
| Hierarchical allocation | Lets routers summarise millions of addresses in one entry |
| Private address ranges | Lets separate networks reuse the same addresses internally |
| NAT | Lets many private devices share one scarce public address |

## IPv6: your design, more or less

IPv6 addresses are 128 bits: about 3.4 × 10<sup>38</sup> of them, enough to give every device on Earth trillions of addresses. With IPv6:

- Every device can have a **globally unique, hierarchical** address: registry → ISP → customer → subnet → device. That's essentially the phone-number design you proposed.
- **NAT isn't needed.** A home gets its own block from the ISP (commonly a `/56` or `/64`), and each device gets a public address inside it. A firewall on the router still blocks unwanted incoming traffic.
- Addresses still change when you move to another network, for the reason in point 2 above.

Adoption is substantial but incomplete, so most devices today speak both IPv4 and IPv6 ("dual stack") and use whichever the other side supports.

## Slips to avoid

- **"NAT fixes hierarchy."** NAT works around address scarcity. Hierarchy is a separate idea.
- **"My public IP is mine."** Under CGNAT, it's your ISP's, shared with other customers.
- **"Private addresses are dropped by my home router."** Your phone's subnet check usually stops them before they leave; outside the home, no public router has a route for them.
- **"An IP address is a device's permanent ID."** It describes where the device is connected and changes when it moves.

## Check yourself

1. Why does every home use addresses like `192.168.1.x`, and why don't they conflict?
2. Describe what the router changes in an outgoing datagram, and how it knows where to send the reply.
3. Why can't someone on the Internet start a connection to your laptop at home?
4. Using the IMEI and phone-number comparison, explain why IP addresses can't be assigned at the factory.
5. How would you check whether your ISP uses carrier-grade NAT?

> [!success]- Answers
> 1. They're private (RFC 1918) addresses, set aside for internal use and never routed on the public Internet, so each home's set is isolated from every other's.
> 2. It replaces the private source address and port with its public address and a unique outside port, recording the mapping. The reply arrives at that outside port; the table says which inside device and port it belongs to.
> 3. There's no translation-table entry for an unsolicited incoming datagram, so the router doesn't know which inside device it's for.
> 4. The IMEI (factory, flat, says nothing about location) is like a MAC address. The phone number is assigned by the operator you join, which is why it can be hierarchical; a factory doesn't know which network a device will join.
> 5. Compare the router's WAN address (admin page) with the address a "what is my IP" site shows. If they differ, or the WAN address is private or in `100.64.0.0/10`, you're behind CGNAT.

---
← [[m02-07-mac-addresses-and-arp]] · [[m02-internet|Module 2]] · Next: [[m02-09-routing-across-the-internet]] →
