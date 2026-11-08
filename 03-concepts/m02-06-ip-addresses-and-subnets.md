---
type: concept
module: 2
unit: 1
order: 6
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 6. IP addresses, subnets and the first forwarding decision

*Where we are: the laptop has joined the network and has its four settings. Soon it will have a destination address for `bhara.com`. Before sending anything, it faces its first decision: can I deliver this myself, or must I hand it to the router?*

## The problem: near or far?

Every time your laptop sends a datagram, it must answer one question first: **is the destination on my own local network, or somewhere else?**

- If it's **local** (your father's phone on the same Wi-Fi, a printer), the laptop can deliver it directly across the local link.
- If it's **somewhere else** (a web server), the laptop can't reach it directly. It must hand the datagram to a router that can carry it further.

To answer that question, the laptop needs two things: a way to read the destination address, and a rule for which addresses count as "local." That's what IP addresses and subnet masks provide.

## What an IPv4 address is

An IPv4 address is a 32-bit number. People write it as four 8-bit pieces in decimal, separated by dots: `192.168.1.20`. Each piece is 0 to 255.

Two precise points:

- **An address belongs to a network interface, not a machine.** A laptop with Wi-Fi and Ethernet both active has two addresses. A router, which connects several networks, has one address on each of them.
- **An address has structure.** The first part says which network the interface is on; the rest says which interface on that network. The first part is the **network prefix**; the rest is the **host part**. It's like a phone number: the area code says which region, and the rest says which line.

> [!question] You asked (ChatGPT)
> "So, the IP address has meaning also, correct? Each part of it has separate meaning. But why do it need an extra number like subnet mask to identify local network and host? What do you mean by local subnet? Why can't the IP address alone identify whether the destination belongs to the same network or not?"

Yes, it has meaning: prefix plus host part. The catch is that **the boundary between them isn't fixed**, and the address alone doesn't say where it is.

## Why the boundary has to be stated

It used to be implied. Before 1993, IPv4 used *classes*: the first few bits of an address fixed its prefix length at 8, 16 or 24 bits. That was simple but extremely wasteful. An organisation that needed 2,000 addresses was too big for a 24-bit prefix (254 usable hosts) and so got a 16-bit prefix: 65,534 addresses, most of them never used. The Internet was burning through its addresses.

The fix, introduced in 1993, was **CIDR** (Classless Inter-Domain Routing): a prefix may be *any* length. A network that needs 2,000 addresses gets a 21-bit prefix (about 2,046 hosts) and nothing more. Addresses could be handed out in right-sized blocks.

The price of that flexibility is that you can no longer look at an address and know where its boundary is. `192.168.1.20` could be in a network of 254 addresses or of 65,534. So the boundary has to be **stated separately**, and that's what the subnet mask does.

## The subnet mask

A **subnet mask** is a 32-bit number with ones for the prefix bits and zeros for the host bits. `255.255.255.0` is 24 ones followed by 8 zeros, so it says "the first 24 bits are the network." The short way to write the same thing is **prefix length** notation: `192.168.1.20/24`.

The set of all addresses that share your prefix is your **local subnet**. With `192.168.1.20/24`, it's `192.168.1.0` to `192.168.1.255`.

### How the laptop uses it

The laptop applies the mask to both addresses with a bitwise AND (keep the prefix bits, zero out the host bits) and compares the results.

```
Laptop       192.168.1.20   = 11000000.10101000.00000001.00010100
Mask         255.255.255.0  = 11111111.11111111.11111111.00000000
AND →        192.168.1.0    ← my network

Father's phone 192.168.1.35  AND mask → 192.168.1.0   same → LOCAL
bhara.com      203.0.113.40  AND mask → 203.0.113.0   different → REMOTE
```

(`203.0.113.40` is a documentation example, not Bhara's real address.)

> [!question] You asked (ChatGPT)
> "What is a subnet mask and why is it needed? Why does the laptop needs to know about a destination when it connects to a network? It isn't sending or receiving any packet yet, right?"

Right, it isn't sending anything yet when it receives the mask. It doesn't need to know any destination in advance. It needs the *rule*, so that whenever a destination appears later, it can decide immediately. DHCP hands over the mask the same way a new employee is told "anything for the third floor goes in the internal mail; anything else goes to the post room" on their first day, before they've written a single letter.

## The default gateway

If the destination is remote, where does the datagram go? To the **default gateway**: the router's address on your local network.

> [!question] You asked (ChatGPT)
> "What is a default gateway? Is it the IP address of the connected router?"

Yes, in a home: it's the IP address of the router's *local-side* interface (`192.168.1.1`). Your router also has a second address on its ISP side, which is a different interface.

The laptop doesn't need to know the route to `bhara.com`. It only needs the first step: "anything not local goes to `192.168.1.1`." The router takes it from there (note 9).

Under the hood, the laptop keeps a small **routing table**. On a typical home laptop it has two important lines:

| Destination | Meaning |
|---|---|
| `192.168.1.0/24` → deliver directly (on-link) | My local subnet |
| `0.0.0.0/0` → via `192.168.1.1` | Everything else (the default route) |

`0.0.0.0/0` has a prefix of zero bits, so it matches *every* address. When more than one line matches, the most specific one (the longest prefix) wins. That rule, **longest prefix match**, is exactly what routers use too.

## Does local traffic go through the router?

> [!question] You asked (ChatGPT)
> "While transmitting data to a local network device, doesn't the data packet travel to the router? Does it directly travel to the destination device?"

It's not *routed*. Your laptop addresses the frame directly to your father's phone (note 7 shows how it learns the phone's hardware address). Physically, though, on Wi-Fi the frame goes from your laptop to the access point and from the access point to the phone, and in a home the access point is inside the router box. So the data may pass *through the same box*, but only the access point and switch functions handle it. The router function, the part that reads IP addresses and forwards between networks, is never used.

That's an important distinction: a box being on the path doesn't mean its routing function is involved.

## Addresses with special meanings

| Address | Meaning |
|---|---|
| `127.0.0.1` | This machine itself (loopback). Never leaves the computer. |
| `0.0.0.0` | "No address yet" (as in DHCP Discover), or "any address". |
| `255.255.255.255` | Broadcast to everyone on the local link. |
| `192.168.1.0` (in a /24) | The network itself; not given to a device. |
| `192.168.1.255` (in a /24) | Broadcast to that subnet; not given to a device. |
| `169.254.x.x` | Self-assigned because DHCP failed. |
| `10.x`, `172.16–31.x`, `192.168.x` | Private ranges (note 8). |

## Could we do without the mask?

> [!question] You asked (ChatGPT)
> "Let's let's explain an imaginary scenario where there is no MAC address, no subnet mask, only unique hierarchical IP address, which different part. Defines different information like a phone number. If we use it, what's the problem that arises?"

For the subnet-mask half of this question: you could, by making the boundary fixed. If the address format said "the first 64 bits are always the network, the last 64 always the host," nobody would need to be told the boundary. The cost is flexibility: every network gets the same size slot whether it has 3 devices or 3 million.

The interesting part is that **IPv6 almost does what you proposed.** IPv6 addresses are 128 bits (written in hexadecimal, like `2001:db8:85a3::8a2e:370:7334`), and a local network (a subnet) is almost always a `/64`: 64 bits for the network, 64 for the host. With 128 bits there's so much room that wasting space no longer matters. Prefix lengths still exist for routing between larger networks, but on your local network the boundary is effectively fixed. Your instinct matches where the designers ended up once address scarcity was gone. IPv4's mask exists because 32 bits were too few to waste.

## Slips to avoid

- **"An IP address identifies a device."** It identifies an interface, and it describes where that interface is connected.
- **"The mask is part of the IP address."** It's a separate piece of configuration that says where the address's boundary is.
- **"Local traffic goes through the router."** It may pass through the same box, but it isn't routed.

## Try it (Windows)

```
ipconfig
route print -4
```

In `route print`, find the line with Network Destination `0.0.0.0` and Netmask `0.0.0.0`. Its Gateway is your default gateway. Find the line for your own subnet: its Gateway says *On-link*, meaning "deliver directly."

## Check yourself

1. Your laptop is `10.0.5.20/16`. Is `10.0.200.7` local? Is `10.1.5.20`? Show the reasoning.
2. Why couldn't the IP address alone tell the laptop where its network boundary is?
3. What is the default gateway, and why doesn't the laptop need to know the full route to a server?
4. What does `0.0.0.0/0` match, and why doesn't it override the local-subnet entry?
5. Your laptop sends a file to your father's phone on the same Wi-Fi. Is the router's routing function used?

> [!success]- Answers
> 1. /16 means the first two numbers are the network: `10.0`. `10.0.200.7` → `10.0`, local. `10.1.5.20` → `10.1`, remote.
> 2. Since CIDR (1993), prefixes can be any length, so the boundary isn't implied by the address and must be stated by the mask or prefix length.
> 3. The router's address on the local network. The laptop only needs the first step; each router decides the next.
> 4. Every address (zero prefix bits). Longest prefix match: the more specific `/24` entry wins for local addresses.
> 5. No. The frame goes laptop → access point → phone, addressed directly to the phone's hardware address. The router function never reads it.

---
← [[m02-05-joining-a-network-dhcp]] · [[m02-internet|Module 2]] · Next: [[m02-07-mac-addresses-and-arp]] →
