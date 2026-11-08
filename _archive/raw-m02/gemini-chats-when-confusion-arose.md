Q: Tell me the relation between IP address and subnet mask and why they are needed in IPv4 format? Go in sequential order. I mean, let's say IP address and port number is needed to identify the host and the process running within the host. Then what? What more is needed?

A: You have correctly established the first layer of the communication stack: an **IP address** identifies the host, and a **port number** identifies the specific process (application) running inside that host. Together, they form a socket (`IP:Port`) for end-to-end delivery.

  

However, once a packet leaves a process, a critical routing dilemma arises. Here is the sequential breakdown of what happens next, why the subnet mask is required, and what additional pieces are needed to complete the journey.

  

### Step 1: The Routing Dilemma ("Then What?")

Knowing the destination IP address tells you _who_ you want to talk to, but it does not tell your device **how to reach them**.

  

When your computer generates a packet for `142.250.190.46`, it faces an immediate question:

  

- Is this destination device sitting on the **same local network** (connected to the same switch or Wi-Fi router) so I can talk to it directly?
    
      
    
- Or is it on a **remote network** (like the internet), meaning I must hand it over to a gateway router to forward it across the world?
    
      
    

An IP address alone cannot answer this question because a 32-bit IPv4 address has no built-in boundary marker showing where the "network" ends and the "host" begins.

  

### Step 2: The Subnet Mask as a Boundary Lens

This is where the **subnet mask** enters.

  

- **The Mechanism:** A subnet mask is a 32-bit number paired with an IP address (e.g., IP: `192.168.1.50`, Subnet Mask: `255.255.255.0`).
    
      
    
- **The Bitwise Operation:** Your device performs a bitwise **AND** operation between its own IP address and its subnet mask to isolate the **Network ID**. It does the same with the destination IP address.
    
      
    - If the resulting Network IDs match, the destination is **local**.
        
          
        
    - If they do not match, the destination is **remote**.
        
          
        

Without the subnet mask, an IPv4 address is just a flat 32-bit number. The subnet mask acts as the decoding template that tells routers and hosts how to slice that number into a Network portion and a Host portion.

  

### Step 3: What More Is Needed? (The Final Layers)

Once the subnet mask determines whether a destination is local or remote, additional components are required to actually move the data across the physical medium:

  

1. **The Default Gateway (Router IP):**
    
      
    - If the subnet mask reveals that the destination IP is on a _remote_ network, your device realizes it cannot deliver the packet directly. It needs a "bouncer" or exit point—the **Default Gateway**—which is the local IP address of your router. Your device addresses the packet's physical delivery to this router instead.
        
          
        
2. **MAC Addresses (Layer 2 Data Link):**
    
      
    - IP addresses are logical (Layer 3). But physical network interfaces (NICs) on a local network wire or airwave only understand hardware **MAC addresses**.
        
          
        
    - Once the subnet mask tells you a device is local (or points you to the gateway router), your operating system uses the **ARP (Address Resolution Protocol)** to translate that IP address into a physical MAC address.
        
          
        
3. **Physical Framing and Transmission:**
    
      
    - Finally, the network card wraps the IP packet inside an Ethernet frame (complete with source and destination MAC addresses) and converts it into electrical pulses, light signals, or radio waves to transmit it physically.
        
          
        

### Summary Checklist of the Sequence

1. **IP + Port:** Identifies the destination machine and application.
    
      
    
2. **Subnet Mask:** Analyzes the IP to draw the line between Network ID and Host ID, revealing if the target is local or remote.
    
      
    
3. **Default Gateway:** Takes over if the subnet mask proves the target is remote.
    
      
    
4. **ARP & MAC Address:** Translates the IP into hardware addresses for final physical delivery across the local wire.


Q: Let's say, my phone's IP address is 192.168.0.1 and my father's phone's IP address is 192.168.0.2 who is connected to the same Wifi. My mother's phone's IP address is 192.168.0.1 but she is connected to a different wifi. All of our subnet is same 255.255.255.0. What happens if I tried to send a message to both of them over internet?

  

Also, why the NICs configured to understand MAC address only? Why not IP address directly? Because that would result in not needing the MAC address anymore.

  

And I know that router acts as a DHCP server when a device is connected to it. It serves the basic configuration like the devices IP address, subnet mask, default gateway IP address, DNS resolver IP address. Why doesn't it serve the default gateway's MAC address also? This will result in not needing the ARP process anymore


A: Here is the detailed, rigorous breakdown of your three scenarios and networking questions.

  

### Part 1: The IP Conflict and the "Internet" Routing Scenario

Your scenario sets up a fascinating set of conditions:

  

- **Your Phone:** `192.168.0.1` (Subnet: `255.255.255.0`)
    
      
    
- **Father's Phone:** `192.168.0.2` (Same Wi-Fi)
    
      
    
- **Mother's Phone:** `192.168.0.1` (Different Wi-Fi)
    
      
    

Before looking at the message, a **critical networking anomaly** exists here: `192.168.0.1` is almost universally reserved as the **Default Gateway (the router itself)** on home networks. If your phone is manually configured as `192.168.0.1`, you have an immediate **IP address conflict** with your own router, which will break local network operations. Assuming that conflict is somehow bypassed, let's look at what happens when you try to message both over the internet:

  

#### 1. Messaging Father (`192.168.0.2` on the same Wi-Fi)

- **The Subnet Check:** Your phone checks its subnet mask (`255.255.255.0`) against `192.168.0.2`. It determines the destination is **local**.
    
      
    
- **The Result:** Even if you think you are sending a message "over the internet" (via an app like WhatsApp), if the app or local routing attempts to hit that local IP, your device looks up his MAC address via ARP and sends it directly over the local airwaves.
    
      
    
- However, if the messaging app routes traffic through an external cloud server (e.g., WhatsApp servers on the internet), your phone sends the packet to the router, which forwards it out to the ISP, up to the cloud server, and right back down to your father's phone—even though he is sitting in the next room.
    
      
    

#### 2. Messaging Mother (`192.168.0.1` on a different Wi-Fi)

- **The Private IP Wall:** `192.168.x.x` addresses are **private IP addresses** (defined by RFC 1918). They are strictly local and **not routable on the public internet**. Every home router in the world uses ranges like `192.168.0.x`.
    
      
    
- **The Result:** If your phone tries to send a packet directly to `192.168.0.1` over the internet, your packet hits your local gateway. Your router looks at the destination IP (`192.168.0.1`), realizes it's a private IP, and **drops the packet**.
    
      
    
- Furthermore, since her phone is also `192.168.0.1` on her own local network, she is hidden behind _her_ router's public IP address via NAT (Network Address Translation). You cannot reach her private IP from the outside world without a VPN, a public IP, or a central relay server (like a chat server) that both phones connect to outbound.
    
      
    

### Part 2: Why NICs Understand MAC Addresses Instead of IP Addresses Directly

If network interface cards (NICs) operated purely on IP addresses, hardware would theoretically be simpler—so why isn't it designed that way?

  

1. **Layer Independence and Flexibility (The OSI Model):**
    
      
    - **IP (Layer 3)** is _logical_ and _mutable_. It changes dynamically based on where you are. If you take your laptop from home (`192.168.1.5`) to a coffee shop (`10.0.5.20`), your IP address changes.
        
          
        
    - **MAC (Layer 2)** is _physical_ (burned into the silicon of the NIC). It represents the permanent hardware identity.
        
          
        
    - If NICs used IP addresses directly, changing networks would require constantly reprogramming the hardware chip's logical identifier rather than just updating a software-assigned IP configuration.
        
          
        
2. **Multiplexing Multiple Protocols:**
    
      
    - A physical wire or Wi-Fi channel doesn't just carry IPv4 traffic. It might carry IPv6, ARP, Bluetooth packets, or older protocols simultaneously.
        
          
        
    - The Layer 2 MAC address acts as a universal wrapper that delivers data frames across the physical link _regardless_ of what higher-layer protocol (IPv4, IPv6, etc.) is packed inside it. Without MAC addresses, the hardware would have to natively understand the logic of every single network-layer protocol ever invented.
        
          
        

### Part 3: Why Doesn't DHCP Serve the Gateway's MAC Address?

It seems logical that since DHCP gives you the Gateway IP address, it could easily include the Gateway's MAC address in the packet and save you an ARP lookup. It doesn't, for several structural reasons:

  

1. **Strict Layer Separation:**
    
      
    - DHCP is an application/network-layer protocol running over UDP. MAC addresses belong strictly to the Data Link Layer (Layer 2). DHCP's sole design mandate is handing out _logical_ Layer 3 configurations (IP, subnet, gateway, DNS). Keeping Layer 2 discovery separate maintains the modularity of the networking stack.
        
          
        
2. **High Availability and Router Redundancy (VRRP/HSRP):**
    
      
    - In enterprise networks (and advanced home/commercial setups), the "default gateway" is often not a single physical router. Network engineers use protocols like **VRRP (Virtual Router Redundancy Protocol)** or **HSRP**.
        
          
        
    - In these setups, multiple physical routers share a _virtual_ IP address and a _virtual_ MAC address. If the primary router crashes, the backup router instantly takes over that virtual MAC address, and traffic keeps flowing without clients needing to re-run DHCP. If DHCP hardcoded a specific physical router's MAC address into client machines, high-availability failover would break.
        
          
        
3. **ARP is Cheap and Automatic:**
    
      
    - Finding a MAC address via ARP takes a fraction of a millisecond via a local broadcast (`Who has 192.168.0.1?`). Your device only needs to do this once when it joins the network or when the ARP cache entry expires. Because it happens automatically and rarely, there is no performance penalty that justifies bloating the DHCP protocol.



Q: Do you know I have been trying to understand all of this for a long long time and I'm still not satisfied. Every time I ask this type of question anywhere, I hit a wall, like layer 2, layer 3. It's like, just for maintaining the layers or the rules, we are sacrificing convenience or simplicity.

  

Anyway, let's correct my mistake and let's say my IP address is 192.168.0.2 and my father's is 192.168.0.3. My mother's IP address, the private IP address in another network, is 192.168.0.2.

1. You said that this packet will be dropped by my router but how will it know that this address is not in our own network? How will it identify that?
2. From what you said I am guessing that there are two types of IP addresses: public and private. The private one is assigned maybe by the router and I am confused about the public one. My question is: how can I actually send a message to my mother? Why are there two types of IP addresses? Just keeping the public IP addresses for all the devices, isn't it simple? Moreover what about if instead of using IP addresses overall we use MAC addresses for everything? Isn't it simple?


A: It is completely normal to hit that wall. For years, computer science education teaches networking as a stack of rigid, abstract rules ("Layers 1 through 7") that feel like bureaucratic red tape invented just to torment students.

  

When you look at it and think, _"Why didn't they just design this the simple way?"_—you are asking the exact right engineering questions. Let’s strip away the textbook jargon and look at _why_ these complexities exist, starting with what happens in your specific scenario.

  

### Part 1: What happens when you try to send a message to `192.168.0.2` (your mother's private IP)?

Your router doesn't even get the chance to drop it because **your own phone stops you first.**

  

1. **The Subnet Trap:** Your phone looks at its own subnet mask (`255.255.255.0`) and sees the destination IP (`192.168.0.2`). Because the first three blocks match your network, your phone's operating system says: _"Aha! This device is on our local Wi-Fi network with us."_
    
      
    
2. **The Local Search:** Your phone completely ignores the internet router and immediately broadcasts a local ARP message over your Wi-Fi: _"Hey everyone on this local Wi-Fi, who has IP `192.168.0.2`?"_
    
      
    
3. **The Result:**
    
      
    - Since _your_ phone is already configured as `192.168.0.2`, your phone might reply to itself ("That's me!"), or if your father's phone is `192.168.0.3`, maybe another device responds, but **your mother's phone never hears it** because she is on an entirely different Wi-Fi network miles away.
        
          
        
    - Her private IP address only has meaning inside _her_ home network. Across the internet, private IPs are completely invisible and unroutable.
        
          
        

To actually send a message to your mother, you cannot use her private IP. Your messaging app (like WhatsApp or Messenger) connects your phone _outbound_ to a public cloud server on the internet, and your mother's phone connects to that same server. The server acts as a middleman, collecting your message and handing it to her.

  

### Part 2: Why two types of IP addresses (Public vs. Private)? Why not all public?

You asked: _"Just keeping the public IP addresses for all devices, isn't it simple?"_

  

In an ideal world, yes, it would be simpler. But we ran out of math.

  

- **The IPv4 Math Problem:** Standard IPv4 addresses are 32-bit numbers. That means there are only about **4.3 billion** unique IPv4 addresses in existence ($2^{32}$).
    
      
    
- **The Scale Mismatch:** Today, humanity has tens of billions of devices—smartphones, laptops, smart TVs, refrigerators, servers, and routers. If every single device needed its own unique public IP address, we would have run out of addresses back in the 1990s.
    
      
    
- **The Private IP Solution:** To save the internet, engineers carved out specific ranges of IP addresses (like `192.168.x.x` or `10.x.x.x`) and declared them **Private**.
    
      
    - This means **millions of different homes around the world can use the exact same IP addresses (`192.168.0.2`) simultaneously.**
        
          
        
    - They don't conflict because they are hidden behind a home router using **NAT (Network Address Translation)**, which translates your private local traffic into a single shared public IP address when going out to the internet.
        
          
        

### Part 3: Why not use MAC addresses for _everything_? (The ultimate "simple" question)

This is the holy grail of networking questions: _If MAC addresses permanently identify hardware, why don't we just throw away IP addresses and use MAC addresses globally for everything?_

  

To understand why this fails, think of the difference between a **Fingerprint** and a **Mailing Address**.

  

- **A MAC Address is a Fingerprint:** It is unique to the physical piece of hardware. It tells you _who_ or _what_ something is, but it tells you **nothing about where it is located**.
    
      
    
- **An IP Address is a Mailing Address:** It has a geographical and structural hierarchy (Country $\rightarrow$ ISP $\rightarrow$ City $\rightarrow$ Street $\rightarrow$ House).
    
      
    

#### Why a MAC-only internet would crash the world:

Imagine trying to mail a letter to your friend in another country, but instead of writing a structured mailing address (`Country, City, Street, House`), you write down your friend's **unique physical fingerprint**.

  

If the postal service only used fingerprints:

  

1. Every mail sorting office on Earth would need a massive global ledger containing the fingerprint of every single human being on the planet.
    
      
    
2. When a letter arrives at a post office in a foreign country, the postman would look at the fingerprint and have **zero structural clues** about which direction or which country that person is currently standing in. They would have to broadcast a megaphone message to every post office on Earth: _"Does anyone have this fingerprint?"_
    
      
    
3. The internet would grind to a complete halt because routers would need petabytes of memory to store the MAC address of every device on Earth, and every packet would flood the globe looking for a random hardware ID.
    
      
    

### Summary

- **IP Addresses (and Subnets)** provide a **hierarchical routing system** so data can find its way across complex, changing global networks (like finding a city and street).
    
      
    
- **MAC Addresses** provide a **flat identification system** used only for local, physical delivery once the packet has successfully arrived at the correct local destination (like ringing a specific doorbell on that street).


Q: What do you mean by single shared IP address? An shared IP address of router+device?

  

So, for router to device communication, MAC address is used. For router to router and to other public devices, IP address is used, correct?


A: Your intuition here is **spot on**. You have just hit upon the exact operational split that defines how the internet works.

  

Let's address your two questions directly.

  

### 1. What do I mean by "single shared IP address"? (Router + Device)

When people in a home network talk about their "public IP address," they are referring to the **public IP address of the router**, not the individual devices.

  

- **Inside your home:** Every device (your phone, your father's phone, your laptop, your smart TV) has a private IP address (`192.168.0.x`).
    
      
    
- **Outside your home (on the internet):** To your Internet Service Provider (ISP) and website servers (like Google or YouTube), **your entire household looks like a single device with one single public IP address** (e.g., `203.0.113.50`).
    
      
    

#### How does NAT (Network Address Translation) make this work?

Imagine an apartment building with one main street address (`123 Main Street`), but individual apartment numbers inside (`Apt 1`, `Apt 2`).

  

- **The Street Address** is your router's **Public IP address**.
    
      
    
- **The Apartment Numbers** are tracked by your router using **Ports** (remember your first point about IP + Port!).
    
      
    

When your phone (`192.168.0.2:55555`) wants to visit Google, your router intercepts the packet, strips off your private IP, replaces it with the router's **Public IP**, assigns a unique outgoing port number (e.g., `Public IP:40001`), and sends it out to the internet.

  

When Google replies to `Public IP:40001`, your router looks at its internal translation table, says, _"Ah, port 40001 belongs to Nasiful's phone!"_, swaps the address back to `192.168.0.2`, and hands it to you. That is how dozens of devices share a single public IP address without getting their data mixed up.

  

### 2. Is MAC used locally and IP used globally?

**Yes, absolutely.** You have completely mastered the core division of labor in networking:

  

- **Inside the local network (Router $\leftrightarrow$ Device, or Device $\leftrightarrow$ Device on the same Wi-Fi):**
    
      
    - Devices use **MAC addresses** for direct physical delivery over the airwaves or Ethernet wires.
        
          
        
- **Across the global internet (Router $\leftrightarrow$ Router $\leftrightarrow$ Destination Server):**
    
      
    - Devices and routers use **IP addresses** (hierarchical routing) to navigate across thousands of miles, multiple ISPs, and global fiber-optic cables.
        
          
        

#### What actually happens at the Router boundary?

When your phone sends a packet to a website across the world:

  

1. Your phone wraps the packet in an Ethernet frame using **MAC addresses** to deliver it across your living room to your Wi-Fi router.
    
      
    
2. The router strips away the local MAC addresses, reads the **IP address**, and uses global routing tables to pass it to the next router down the street.
    
      
    
3. That next router strips away _its_ local frame wrapper, looks at the **IP address**, and passes it to the next router.
    
      
    
4. This repeats all the way across the planet until it reaches the destination server.
    
      
    

MAC addresses are constantly being destroyed and recreated at every single hop along the way just to get the packet across local links. But the **IP address** remains the enduring roadmap from the source to the final destination.



Q: Ok, so routers have public IP addresses. Who assigns them? I mean, home device's private IP addresses are assigned by the router generally, who assigns the public address for the routers? From the destination IP address, routers can determine where to send the packet, the next hop, correct?

A: You have hit the nail on the head again. Yes, **from the destination IP address, routers look at their routing tables and determine the next hop.** That is the exact mechanism by which the internet works.

  

Now let’s answer the question of who assigns those public IP addresses and how the hierarchy flows.

  

### Who assigns the public IP addresses to routers?

It works like a massive, global top-down delegation tree, much like a global registry system:

  

1. **IANA (Internet Assigned Numbers Authority) / ICANN:**
    
      
    - At the very top is a global non-profit organization that manages the pool of all remaining unassigned IP addresses in the world.
        
          
        
2. **RIRs (Regional Internet Registries):**
    
      
    - IANA doesn't hand out IPs to individual users; it hands massive blocks of IP addresses to five massive regional authorities (e.g., APNIC for Asia-Pacific, ARIN for North America, RIPE NCC for Europe).
        
          
        
3. **ISPs (Internet Service Providers):**
    
      
    - Your ISP (like your local broadband provider in Bangladesh) requests and purchases large blocks of public IP addresses from their regional registry (APNIC).
        
          
        
4. **Your Home Router:**
    
      
    - When your home router connects to your ISP (via fiber, DSL, or cable), **your ISP dynamically or statically assigns a single public IP address to your router's WAN (Wide Area Network) port** from their allocated block.
        
          
        

So, just as your router acts as a "mini-administrator" handing out private IPs to your phone and laptop, **your ISP acts as the administrator handing out public IPs to your router.**

  

### How Routers Use Destination IPs to Find the "Next Hop"

Your second statement is entirely correct: **Routers look at the destination IP address to figure out the next hop.**

  

To visualize this without getting bogged down in layer rules, think of a physical mail sorting office:

  

1. When a router receives a packet, it looks only at the **Destination IP address** (e.g., Google's IP).
    
      
    
2. It checks its **Routing Table** (its internal map/phonebook).
    
      
    
3. The routing table doesn't list every single computer on Earth; instead, it lists _ranges_ or networks (e.g., _"If you want to reach IP addresses starting with 142.250.x.x, send it out to Router B down the street"_).
    
      
    
4. The router slaps a new local MAC address wrapper on the packet, hands it to the next-hop router, and washes its hands of it.
    
      
    

That next router repeats the exact same process: look at the destination IP, check the routing table, find the next hop, and pass it along until it hits the final destination server.


Q: Is it possible to know the routers that my router "knows"?

A: Yes, absolutely! You can discover the routers that your home router knows—both the **immediate next router** it hands your traffic to, and the entire **chain of routers** it hops across to reach any destination on the internet.

  

Because a home router doesn't store a map of the _entire_ global internet, it relies on a hierarchy of connections. You can easily map these out right from your computer or phone using built-in network discovery tools.

  

### 1. The Immediate "Next Hop" (Your ISP's Gateway)

Your home router only directly "knows" a few local neighbors on your ISP's network. The very first router your home router forwards all your internet traffic to is called its **WAN Default Gateway**.

  

To find this immediate next router:

  

- **Log into your router's web admin page** (usually by typing `192.168.0.1` or `192.168.1.1` in a browser).
    
      
    
- Look at the **WAN** or **Internet** status section.
    
      
    
- You will see your **Public/WAN IP**, as well as the **Default Gateway IP**. That Gateway IP is the specific router inside your ISP's regional center that your home router talks to directly.
    
      
    

### 2. Discovering the Chain of Routers (Traceroute)

If you want to see all the routers your traffic passes through step-by-step from your living room to a remote server, you can run a **traceroute**.

  

Traceroute sends packets with increasing "Time-To-Live" (TTL) limits. Each router along the path drops the packet when the limit hits zero and sends back an error message saying, _"I am Router X, and I dropped your packet."_ Your device collects these replies to draw a map of the exact path.

  

#### How to run it:

- **On Windows (Command Prompt):**
    
      
    
    DOS
    
    ```
    tracert google.com
    ```
    
- **On Mac / Linux (Terminal):**
    
      
    
    Bash
    
    ```
    traceroute google.com
    ```
    
- **On Android / iPhone:** You can download free network utility apps like _Fing_ or _HE.NET Network Tools_ to run a traceroute.
    
      
    

#### Reading the output:

When you run it, you will see a numbered list of hops:

  

1. **Hop 1:** Your local home router (`192.168.0.1`).
    
      
    
2. **Hop 2:** Your ISP's local gateway router (the first router outside your house).
    
      
    
3. **Hops 3–6:** Core routing infrastructure inside your ISP (e.g., regional switches in your city/country).
    
      
    
4. **Hops 7+:** Transit networks, submarine cable landing stations, and border routers of destination networks (like Google or Cloudflare).
    
      
    

### How Big Routers Share Maps (BGP)

While your home router simply forwards everything it doesn't recognize to your ISP, **enterprise and ISP routers** communicate using a protocol called **BGP (Border Gateway Protocol)**.

  

Through BGP, core internet routers constantly talk to neighboring routers, exchanging lists of millions of IP address ranges (Autonomous System numbers) so they always know the shortest and healthiest path across the global network.


Q: Explain it:

C:\Users\ASUS>tracert niruddeshjatra.space

  

Tracing route to niruddeshjatra.space [216.198.79.1]

over a maximum of 30 hops:

  

  1     1 ms     1 ms     1 ms  192.168.1.1

  2     5 ms     2 ms     4 ms  10.8.66.1

  3     4 ms     2 ms     2 ms  103.121.37.9

  4     4 ms     2 ms     1 ms  10.20.241.1

  5     4 ms     1 ms     1 ms  10.9.6.81

  6     8 ms     5 ms     5 ms  10.198.9.73

  7    30 ms    13 ms    15 ms  10.198.9.53

  8     *        *        *     Request timed out.

  9     *        *        *     Request timed out.

 10     *        *        *     Request timed out.

 11     *        *        *     Request timed out.

 12     *        *        *     Request timed out.

 13     *        *        *     Request timed out.

 14    13 ms    12 ms    11 ms  216.198.79.1

  

Trace complete.

  

How to trace and learn from wireshark? I mean, how to use it for learning networking concepts and exploring?


A: Here is a breakdown of your traceroute output, followed by a guide on how to leverage Wireshark as a hands-on learning laboratory to see these exact concepts in real time.

  

### Part 1: Explaining Your `tracert` Output

Your traceroute tells a fascinating story of your packet's journey from your computer to the server hosting `niruddeshjatra.space` (`216.198.79.1`).

  

- **Hop 1 (`192.168.1.1` - 1 ms):**
    
    This is your home Wi-Fi router. Your computer talks to it instantly (1 millisecond response time). This is local wireless/wired communication using MAC addresses.
    
      
    
- **Hops 2 to 7 (Internal ISP Infrastructure - `10.x.x.x` and `103.x.x.x`):**
    
    Notice how these intermediate IPs start with `10.x.x.x`? Just like your home network uses private `192.168.x.x` addresses, your ISP uses private IP ranges (`10.x.x.x`) internally to route traffic across their massive carrier network before pushing it out to the public internet.
    
      
    
- **Hops 8 to 13 (Request Timed Out `* * *`):**
    
    Why did it stop replying? **Many routers on the internet are configured to ignore traceroute probes.** Routers are built to push heavy web traffic as fast as possible, not to waste CPU cycles answering ping/traceroute tests. A timeout **does not mean your packet stopped moving**; it just means that specific router chose to stay silent while passing your packet along.
    
      
    
- **Hop 14 (`216.198.79.1` - 11 ms):**
    
    The destination! This is the public IP address of the server hosting the website, successfully reached after 14 hops.
    
      
    

### Part 2: How to Use Wireshark for Learning Networking

Wireshark is the ultimate tool to shatter the "wall" of networking theory because it allows you to **see, touch, and inspect the actual bytes** of every packet leaving or entering your machine.

  

Instead of treating networking as abstract rules, here is how to use Wireshark as a practical learning laboratory.

  

#### 1. The Setup (Seeing the Invisible)

- Download and install **Wireshark** (it's free and open-source).
    
      
    
- Open Wireshark and choose your active network interface (e.g., your **Wi-Fi** or **Ethernet** adapter) to start capturing live traffic.
    
      
    
- _Warning:_ You will see thousands of packets per second. To keep from getting overwhelmed, use the **Display Filter** bar at the top.
    
      
    

#### 2. Practical Exercises to Master Each Concept

- **Exercise A: Witness the ARP Process (MAC vs. IP)**
    
      
    - _Filter:_ Type `arp` in the filter bar.
        
          
        
    - _Action:_ Open your command prompt and type `arp -d` to clear your cache, then ping a device on your local network.
        
          
        
    - _What to look for:_ You will see an ARP broadcast packet saying: _"Who has [IP]? Tell [Your IP]"_ and a reply giving the physical **MAC address**. This proves everything we discussed about needing MAC addresses for local delivery.
        
          
        
- **Exercise B: Catch the DNS Lookup (Translating Names to IPs)**
    
      
    - _Filter:_ Type `dns` in the filter bar.
        
          
        
    - _Action:_ Open your browser and go to a website you haven't visited recently (e.g., `example.com`).
        
          
        
    - _What to look for:_ Click on the packet and expand the **Domain Name System** section in the lower panel. You will see your computer asking a DNS server, _"What is the IP address for example.com?"_ and the server replying with the numbers.
        
          
        
- **Exercise C: Inspect TCP 3-Way Handshake & Ports**
    
      
    - _Filter:_ Type `tcp.flags.syn == 1` or filter by a specific website using `ip.addr == 216.198.79.1`.
        
          
        
    - _Action:_ Visit a website or reload a page.
        
          
        
    - _What to look for:_ Look at the source and destination **Port numbers** and the TCP flags (`SYN`, `SYN-ACK`, `ACK`). This shows you how two processes establish a connection before sending application data.
        
          
        
- **Exercise D: Trace a Traceroute Live**
    
      
    - _Filter:_ Type `icmp` (Internet Control Message Protocol).
        
          
        
    - _Action:_ Run `tracert google.com` in your command prompt while Wireshark is recording.
        
          
        
    - _What to look for:_ You will see your computer sending out packets with incrementing TTL values and receiving `Time-to-live exceeded` error messages back from the routers along the path. You are literally watching the traceroute mechanism happen packet-by-packet.
