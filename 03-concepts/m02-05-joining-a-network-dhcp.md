---
type: concept
module: 2
unit: 1
order: 5
written_by: claude
tags:
  - medium
source: "[[kurose-ch01]]"
---
# 5. Joining a network: DHCP

*Where we are: the journey begins. You open your laptop at home and it joins the Wi-Fi. Before it can send anything to `bhara.com`, it needs to know how to behave on this network.*

## The problem: a newcomer with no address

When your laptop connects to your home Wi-Fi, it has a hardware address burned into (or set on) its Wi-Fi card, but it has no IP address for this network. Without one, it can't put a sensible source address on anything it sends, and nobody can reply to it.

> [!question] You asked (ChatGPT)
> "I understood that my laptop needs an IP address to communicate with any other networking device on the internet. But why doesn't it already have an IP address? Why does the router need to hand it one?"

Because an IP address is not a name for the laptop. It describes **where the laptop is connected**. Its first part says which network you're on (note 6 shows how). At home you might be `192.168.1.20`; at a university you might be `10.40.7.112`; on mobile data, something else again. A laptop can't be given its address in the factory, because the factory doesn't know which network it will join. The network that you join has to tell you. (Note 8 goes deeper into why addresses work this way, using your phone-number question.)

So joining a network has two separate steps:

1. **Link connection.** The laptop associates with the Wi-Fi access point and, if the network has a password, proves it knows it. Now the laptop can exchange frames with nearby devices. This is the link layer working.
2. **Network configuration.** The laptop obtains an IP address and the other settings it needs. Usually this is done by **DHCP**.

> [!question] You asked (ChatGPT)
> "So, when a device is connected to a router over Wi-Fi for the first time, it doesn't get an internet connection immediately, correct?"

Correct. "Connected to Wi-Fi" only means step 1 worked. You need step 2, and then the router's own connection to the ISP has to be working. That's why a phone can show full Wi-Fi bars and still say "Connected, no internet."

## What the laptop needs to learn

Four settings, each for a different future decision:

| Setting | Example | What it's for |
|---|---|---|
| Its own IP address | `192.168.1.20` | Its address on this network |
| Subnet mask | `255.255.255.0` | To tell which addresses are local (note 6) |
| Default gateway | `192.168.1.1` | Where to send anything that isn't local (note 6) |
| DNS resolver | `192.168.1.1` | Where to send name lookups (note 10) |

Plus a **lease time**: how long it may use the address before it must renew it.

## Who hands them out: the DHCP server

> [!question] You asked (ChatGPT)
> "What do you mean by DHCP server?"

A **DHCP server** is a program whose job is to hand out network configuration. In a home, it runs inside the router. Your "router" box is really several devices in one:

- a Wi-Fi **access point** (the radio),
- a small **switch** (for the Ethernet ports),
- a **router** (connecting your home network to the ISP),
- a **DHCP server** (handing out configuration),
- a **DNS forwarder** (passing your lookups on),
- a **NAT** device (sharing one public address, note 8).

These are separate jobs that happen to run in one box. In an office, the DHCP server is often a separate machine.

DHCP stands for *Dynamic Host Configuration Protocol*. It's an application-layer protocol that runs over UDP (note 4): the server listens on port 67, the client uses port 68.

## The bootstrap problem: asking for an address without one

> [!question] You asked (ChatGPT)
> "How or when does the laptop search for a DHCP server?"

As soon as the Wi-Fi link is up and the laptop has no valid configuration, it starts DHCP. But there's a puzzle: how do you send a message when you have no address and don't know who to send it to?

DHCP solves this with two tricks:

- **Source address `0.0.0.0`** means "I don't have an address yet."
- **Destination address `255.255.255.255`** means "everyone on this local network." This is a **broadcast**: every device on the link receives it, and only a DHCP server will pay attention.

At the link layer, the frame is also sent to the broadcast hardware address, so every device's network card accepts it.

> [!question] You asked (ChatGPT)
> "How can the DHCP server identify the discovery message sending device? I mean, what is the identification it uses before assigning an IP address? It doesn't have a name to call yet, right?"

Right, it has no IP address and no name. But it does have a **hardware (MAC) address** on its network card, and the link layer can deliver frames using that even before any IP address exists. The DHCP message contains a field holding the client's hardware address, plus a random **transaction ID** so that replies can be matched to requests. The server uses the hardware address to tell clients apart and to remember who got which address.

Remember this. It's one of the strongest reasons the link layer has its own addresses, separate from IP, and it comes back in note 7.

## DORA: the four messages

DHCP's four messages show the "order" part of a protocol (note 1) very clearly.

1. **Discover** (client → everyone): "I need configuration. Is there a DHCP server here?"
2. **Offer** (server → client): "You could use `192.168.1.20`, mask `255.255.255.0`, gateway `192.168.1.1`, DNS `192.168.1.1`, for 24 hours."
3. **Request** (client → everyone): "I accept *that* server's offer of `192.168.1.20`."
4. **Acknowledge** (server → client): "Confirmed. It's yours for the lease period."

> [!question] You asked (ChatGPT)
> "Offer and accept are the two words that goes well. But how can offer be replied with a request? And how can a request be replied with acknowledge, not a response?"
>
> And later: "Yes, not a requirement, rather, a stupidity. They should name them wisely, I think."

The names are confusing; the logic behind the extra step is sound. Two reasons the client sends a *Request* instead of just using the offer:

- **There may be more than one DHCP server.** Each one may send an Offer. The Request is broadcast and names the chosen server, so every server hears the decision. The chosen server commits the address; the others put their offered addresses back in their pools.
- **The server must commit.** An Offer is only a proposal. The address isn't formally leased until the server records it and says so. That's the Acknowledge. Without it, two clients could end up thinking they own the same address.

The same Request message is also used later to *renew* a lease, which is partly why it's called "Request" and not "Accept".

> [!question] You asked (ChatGPT)
> "How does the device automatically choose from the offers?"

The protocol doesn't fix the rule; it's up to the client's software. In practice, most clients simply take the first acceptable offer that arrives.

> [!question] You asked (ChatGPT)
> "How does the DHCP server choose a configuration?"

The server is configured with a **pool** (for example `192.168.1.100` to `192.168.1.200`) and keeps a **lease table** recording which hardware address has which IP address until when. For a new client it picks a free address from the pool. Many servers prefer to give a returning device the same address it had last time. An administrator can also set a **reservation**: "this hardware address always gets `192.168.1.50`."

## Leases and renewal

A lease is temporary on purpose: devices leave without saying goodbye, and the pool would run out if addresses were held forever. Halfway through the lease, the client quietly asks its server to renew. If the server never answers and the lease runs out, the client must stop using the address and start DORA again.

If no DHCP server answers at all, Windows and macOS give themselves an address in the `169.254.x.x` range. If you ever see `169.254` in `ipconfig`, it means "DHCP failed."

## Static configuration

> [!question] You asked (ChatGPT)
> "What do you mean by static configuration?"

Typing the four settings in by hand instead of getting them from DHCP. It's used for servers, printers and network equipment that must always have the same address. The person doing it must make sure the address isn't in DHCP's pool and isn't used by anything else, or two devices will clash. A DHCP reservation gives the same result while keeping everything managed in one place.

## Where the DNS resolver address points

> [!question] You asked (ChatGPT)
> "Are the DNS resolver addresses actually IP addresses of the ISP?"

They can be, but often aren't. At home, DHCP usually hands out the **router's own address** as the DNS resolver. The router then forwards your lookups to the ISP's resolver or a public one such as `1.1.1.1`. Note 10 explains what the resolver actually does.

## Ethernet and mobile data

> [!question] You asked (ChatGPT)
> "You have described it all for Wi-Fi, what about mobile data or Ethernet?"

- **Ethernet:** identical, except step 1 is plugging in a cable instead of associating with an access point. DORA runs exactly the same way.
- **Mobile data:** different. Your phone doesn't do DHCP's DORA over the radio. When it starts a data session, the mobile operator's network assigns it an IP address as part of the operator's own signalling between the phone and the network. The radio link from phone to tower is a direct link with the operator's equipment on the other end, so there's no local network of neighbours and no need to look up a neighbour's hardware address. Everything above the network layer (DNS, TCP, TLS, HTTP) is the same.

## Slips to avoid

- **"Connected to Wi-Fi = on the Internet."** Link connection, network configuration and a working ISP connection are three separate things.
- **"The router gives the laptop an IP address."** More precisely, the DHCP server *running in* the router leases one.
- **"DHCP runs every time I open a website."** It runs when you join a network and when the lease needs renewing.

## Try it (Windows)

```
ipconfig /all
```

Look for: *DHCP Enabled*, *IPv4 Address*, *Subnet Mask*, *Default Gateway*, *DHCP Server*, *DNS Servers*, *Lease Obtained* and *Lease Expires*. That's the whole DORA result on one screen.

```
ipconfig /release
ipconfig /renew
```

Run these with Wireshark capturing and the filter `dhcp`, and you'll see Discover, Offer, Request and Ack go past.

## Check yourself

1. Why can't an IP address be assigned to a laptop at the factory?
2. What four settings does DHCP provide, and what is each one used for later?
3. How does a laptop send a DHCP Discover when it has no IP address, and how does the server tell it apart from other new devices?
4. Why does DHCP need a Request and an Acknowledge after the Offer?
5. You see `169.254.12.7` on your laptop. What happened?

> [!success]- Answers
> 1. An IP address describes where the device is connected; the factory doesn't know which network it will join.
> 2. IP address (its address here), subnet mask (local or not), default gateway (where non-local traffic goes), DNS resolver (where lookups go). Plus a lease time.
> 3. Source `0.0.0.0`, destination broadcast `255.255.255.255`, sent in a broadcast frame. The server identifies it by its hardware (MAC) address in the message, matched by a transaction ID.
> 4. Several servers may offer; the broadcast Request tells all of them which offer was chosen. The Acknowledge is the server committing the lease, so two clients can't both believe they own one address.
> 5. No DHCP server answered, so the OS gave itself a link-local address. It can't reach the Internet.

---
← [[m02-04-ports-sockets-and-udp]] · [[m02-internet|Module 2]] · Next: [[m02-06-ip-addresses-and-subnets]] →
