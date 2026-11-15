---
type: concept
module: 2
unit: 1
order: 7
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 7. Local delivery: MAC addresses, ARP and switches

*Where we are: the laptop has decided "`bhara.com` is remote, so this goes to my gateway, `192.168.1.1`." Now it has to physically get the datagram to that router, across the Wi-Fi.*

## The problem: who on this link should take it?

> [!question] You asked (ChatGPT)
> "The laptop send the packet to its default gateway; the router, fine. But how does it send the packet?"

On Wi-Fi, your laptop's radio transmits, and every device in range hears the signal: the router, your father's phone, the smart TV, your neighbour's laptop if they're close enough. On an older shared Ethernet cable, the same was true electrically. So every transmission needs a label saying **which device on this link is meant to take it**. Everyone else ignores it.

Notice what *can't* do that job: the IP destination address. The datagram's destination is `bhara.com`'s server, which is not on this link at all. The device that should receive this transmission is the router, so the frame needs a second, separate destination: "the next device." Two destinations, for two different questions:

| Address | Question it answers | Changes at each hop? |
|---|---|---|
| IP destination (in the datagram) | Where is this ultimately going? | No |
| Link destination (in the frame) | Which device on *this* link takes it next? | Yes |

## MAC addresses

On Ethernet and Wi-Fi, the link destination is a **MAC address** (*Media Access Control*). It's a 48-bit number written as six pairs of hexadecimal digits, like `3c:22:fb:91:0a:5e`. The first three pairs identify the manufacturer; the manufacturer sets the rest so that each network card it makes has a different one.

Two corrections to things you may have read in the AI chats:

- **"Burned in and permanent" isn't quite true.** The factory value is stored on the card, but software can change it. Modern phones *randomise* their MAC address for each Wi-Fi network by default (Android 10 onwards, iOS 14 onwards) so that shops and airports can't track them from network to network. Your phone may appear with a different MAC address on your home Wi-Fi than on a café's.
- **A MAC address only needs to be unique on one link.** It's a local delivery label, not a global identity.

## The Ethernet frame

Here's what the link layer wraps around a datagram on Ethernet:

| Field | Size | Purpose |
|---|---|---|
| Destination MAC | 6 bytes | Which device on this link takes it |
| Source MAC | 6 bytes | Which device sent it |
| Type | 2 bytes | What's inside: IPv4, IPv6 or ARP |
| Payload | 46–1,500 bytes | The datagram |
| Frame check sequence | 4 bytes | Detects bits corrupted on this link |

The **Type** field is worth noticing: one link can carry IPv4, IPv6 and ARP side by side, and the Type field tells the receiver which one is inside. The link layer doesn't care which.

Wi-Fi frames have a different format with up to four address fields, because the access point sits in the middle of every exchange. The idea is the same: the frame is addressed to the next device on this link.

> [!question] You asked (ChatGPT)
> "Ethernet or Wi-Fi transmission on the local network needs a link-layer destination - why link layer? Again you introduced layers without explaining them or in an intuitive way."

"Link layer" just means the group of rules for getting a frame from one device to the next device *on one link*. Note 3 explains the layer idea; this note is the link layer doing its one job.

## ARP: finding the next device's MAC address

The laptop knows the gateway's IP address (`192.168.1.1`, from DHCP) but not its MAC address. It needs to translate one into the other. For IPv4 that's done by **ARP** (*Address Resolution Protocol*).

1. The laptop broadcasts a frame to the special address `ff:ff:ff:ff:ff:ff`, which every device on the link accepts: *"Who has `192.168.1.1`? Tell `192.168.1.20`."*
2. Every device reads it. Only the one with that IP address replies, directly to the laptop: *"`192.168.1.1` is at `a4:91:b1:07:22:c0`."*
3. The laptop stores the answer in its **ARP cache** for a while, so it doesn't ask again for every frame. The router, having seen the question, also learns the laptop's mapping.

Now the laptop can build the frame:

```
[ dst: router's MAC | src: laptop's MAC | type: IPv4 ]
    [ src IP: 192.168.1.20 | dst IP: 203.0.113.40 | … ]
        [ TCP segment … ]
```

ARP only ever works on one link. A broadcast stops at the router; ARP never crosses the Internet. That's why the laptop asks for the *gateway's* MAC address and never the server's: the server isn't on this link, and its MAC address would be useless here. IPv6 does the same job with a different protocol, *Neighbor Discovery*.

## A misconception to fix: "MAC is local, IP is global"

> [!question] You asked (Gemini)
> "So, for router to device communication, MAC address is used. For router to router and to other public devices, IP address is used, correct?"

Gemini answered "Yes, absolutely. You have completely mastered the core division of labor." **That was wrong**, and it's worth undoing carefully because it sounds so close to right.

The real split isn't *where* (local vs global). It's **per hop vs end to end**:

- **Link addresses are used on every hop**, including router to router. Two routers in an ISP's data centre connected by an Ethernet cable exchange frames with each other's MAC addresses, exactly like your laptop and your home router. (Some links between routers are point-to-point, with only one possible receiver, and their link headers carry no MAC address at all. Either way, the link header describes only that one hop.)
- **IP addresses are used end to end, from the very first hop.** The datagram leaving your laptop already carries the server's IP address as its destination, even while it's crossing your living room.

So at every hop there are *both*: a frame addressed to the next device, carrying a datagram addressed to the final destination. At each router, the old frame is thrown away and a new one built for the next link. The datagram inside keeps its IP addresses the whole way (except where NAT rewrites them, note 8).

## The big question: why two address systems at all?

You asked this three times, to two different AIs, and pushed back when the answers were vague. Here are your questions, then the answer in one place.

> [!question] You asked (Gemini)
> "Also, why the NICs configured to understand MAC address only? Why not IP address directly? Because that would result in not needing the MAC address anymore."

> [!question] You asked (ChatGPT)
> "I still haven't understood why is it convenient for the link-layer to use MAC address instead of IP address? It could use source link IP and next-hop link IP. Then, why not? Look, if it used IP, it didn't even have to go through ARP process."

> [!question] And then (ChatGPT)
> "Reasoning for why MAC address instead of IP address seems dubious to me. … What's the issue if the local delivery mechanism is tightly coupled to IP? … I can't blindly accept anything just engineers decided so."

**First, the honest part: your design would work.** A link layer that addressed frames to "next-hop IP address" is technically possible, and some links already carry IP with no MAC addresses at all (point-to-point links). Separate link addresses are not a law of nature. Gemini's argument ("you'd have to reprogram the chip every time you change network") was weak, and you were right to call it dubious; network cards are configured by software all the time.

So the real question is: what does the separate system buy, and why did it win? Four reasons, strongest first.

**1. The link has to work before you have an IP address.** This is the knockout argument, and you already saw it in note 5. A device joining a network sends its DHCP Discover *before it has any IP address*. That frame has to be delivered to the DHCP server, and the server's reply has to be delivered back to a device with no IP address. If frames were addressed by IP, a brand-new device would be unreachable. Any link layer, including your hypothetical one, needs *some* addressing that works without IP. Once you have that, using it for all local delivery is the simple choice.

**2. History: Ethernet came first, and IP wasn't the only protocol on it.** Ethernet was invented in 1973 and standardised in 1980–83. IP only became the Internet's protocol in 1983, and for another ten years or more office networks carried several network protocols on the same cable: Novell's IPX, Apple's AppleTalk, DEC's DECnet, and IP. A link technology that used IP addresses would have been useless for every other protocol. So Ethernet used its own addresses, and the **Type** field said which protocol was inside each frame. By the time IP won, every network card and switch on Earth spoke MAC. Changing that now would mean replacing all of them to save one small lookup. That's path dependence: it's how things were built, and the switch isn't worth its cost.

**3. Independence still pays today.** The same link carries IPv4 and IPv6 side by side. When IPv6 arrived, not a single switch had to be replaced, because switches never looked at IP addresses at all. A switch that forwarded by IP would have needed upgrading for IPv6. Keeping the link layer ignorant of the network layer is what made that transition possible.

**4. Different lifetimes.** Your IP address changes when you move between networks or renew a lease. Your link address stays put while you do that, so the link keeps working while the IP configuration is being negotiated.

**The cost, stated plainly:** ARP. It's one broadcast question per neighbour, answered in well under a millisecond and then cached. That's the whole price.

> [!question] You asked (Gemini)
> "Why doesn't it serve the default gateway's MAC address also? This will result in not needing the ARP process anymore"

Three reasons it wouldn't save much:

- You'd still need ARP for every *other* local neighbour: your father's phone, the printer, the TV. The gateway is just one of them.
- The DHCP server isn't always the gateway. In offices it's often a separate machine that doesn't know the gateway's MAC address.
- The gateway's MAC address can change. In networks with two routers for backup, the "gateway" is a shared virtual address that moves to the backup router if the main one fails. A MAC address handed out by DHCP would go stale; ARP always finds the current one.

## And why not MAC addresses for everything?

> [!question] You asked (Gemini)
> "Moreover what about if instead of using IP addresses overall we use MAC addresses for everything? Isn't it simple?"

The opposite proposal fails for a different reason: **MAC addresses are flat.** The number on your phone's Wi-Fi chip says who made it, not where it is. Two phones with nearly identical MAC addresses could be in Chattogram and in Lima.

For routers, that's fatal. An IP router can store one entry like "everything starting with `103.121.` goes that way" and cover thousands of devices at once, because addresses that start the same are in the same network. With MAC addresses, there's no such grouping. Every router on the Internet would need a separate entry for every device on Earth, tens of billions of them, updated every time anyone moved. IP addresses are hierarchical by **network** (which ISP, which customer, which subnet), so they can be summarised.

One correction to Gemini's analogy: it called IP a postal address of "Country → City → Street → House." IP's hierarchy follows *networks*, not geography. An ISP's address block is wherever that ISP routes it, which is usually a region but doesn't have to be.

## Switches: delivering frames inside a network

In a wired network, devices plug into a **switch**. A switch reads only link-layer headers and forwards frames by MAC address. It builds its table by itself:

1. When a frame arrives on port 3 from MAC `aa:…`, the switch notes "`aa:…` is on port 3."
2. When a frame for `aa:…` arrives later, it sends it out of port 3 only.
3. If it doesn't know where a destination is yet, or the frame is a broadcast, it sends a copy out of every port except the one it came in on.

No configuration is needed, and the switch never looks at IP. That's the independence from reason 3 above, in one of the cheapest boxes in any computer shop.

## Slips to avoid

- **"MAC between device and router, IP between routers."** Link addresses on every hop; IP addresses end to end.
- **"The laptop asks ARP for the server's MAC address."** It asks for the next hop's: the gateway's, for anything remote.
- **"ARP crosses the Internet."** ARP never leaves the local link.
- **"MAC addresses are permanent identities."** They're local delivery labels, often randomized on phones.

## Try it (Windows)

```
arp -a
```

Your gateway's IP and MAC address will be listed. Then, with Wireshark capturing and the display filter `arp`, run (as administrator) `arp -d *` to clear the cache and `ping 192.168.1.1`. You'll see the "Who has 192.168.1.1?" broadcast and the reply.

## Check yourself

1. Your laptop sends a datagram to `bhara.com`. What are the IP destination and the MAC destination of the frame that leaves the laptop?
2. Correct this sentence: "MAC addresses are used between a device and its router; IP addresses are used between routers."
3. Give the strongest reason the link layer has its own addresses, separate from IP.
4. Why can't MAC addresses replace IP addresses for routing across the Internet?
5. Why does DHCP not just hand out the gateway's MAC address?

> [!success]- Answers
> 1. IP destination: the server's IP address. MAC destination: the gateway's MAC address.
> 2. Link-layer addresses are used on every hop, router to router included; IP addresses are carried end to end from the first hop to the last.
> 3. A device must be reachable before it has an IP address (DHCP runs over the link first), so the link needs addressing that works without IP. History (Ethernet predates IP's dominance and carried many protocols) and independence (IPv6 needed no new switches) support it.
> 4. MAC addresses are flat: they say nothing about location, so routers couldn't group them and would need an entry per device worldwide.
> 5. ARP would still be needed for every other neighbour; the DHCP server may not be the gateway; the gateway's MAC can change (failover), and ARP always finds the current one.

---
← [[m02-06-ip-addresses-and-subnets]] · [[m02-internet|Module 2]] · Next: [[m02-08-public-private-addresses-and-nat]] →
