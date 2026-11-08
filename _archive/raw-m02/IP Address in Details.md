### **The Internet's Address System: IP Addresses Explained**

Okay, so DNS translates names to IP addresses. But what _is_ an IP address, really?

An IP address is a unique identifier for a device on a network. Think of it like a mailing address, but for computers.

There are two versions in use today:

**IPv4**: Looks like `192.168.1.1` (four numbers, 0-255). IPv4 (Internet Protocol version 4) is the older and still most common version. It uses 32-bit addresses, giving us about 4.3 billion possible addresses. That sounds like a lot, but we've essentially run out. Every device needs an IP, and with billions of phones, computers, IoT devices, etc., 4.3 billion isn't enough. This is why we're slowly migrating to IPv6.

**IPv6**: Looks like `2001:0db8:85a3::8a2e:0370:7334` (hexadecimal, way more addresses). IPv6 (Internet Protocol version 6) is the newer version designed to solve IPv4's address shortage. It uses 128-bit addresses, providing an astronomical number of possible addresses — roughly 340 undecillion (that's 36 zeros). Adoption is growing but slow because migrating the entire internet is complex. Most devices now support both IPv4 and IPv6.

---

### **Public vs Private IPs**

Here's something that _A public IP address is globally unique and routable on the internet. It's like your home's street address — there's only one in the world. When you connect to the internet, your ISP assigns you a public IP (sometimes shared, sometimes dedicated). Websites can see this IP, and it's how the internet knows where to send responses back to you. What c_onfuses people: **not all IP addresses are created equal.**

**Public IP**: A public IP address is globally unique and routable on the internet. It's like your home's street address — there's only one in the world. When you connect to the internet, your ISP assigns you a public IP (sometimes shared, sometimes dedicated). Websites can see this IP, and it's how the internet knows where to send responses back to you.

**Private IP**: A private IP address is used within your local network (home, office) and isn't accessible from the internet. Your router assigns these to your devices: laptop gets 192.168.1.10, phone gets 192.168.1.11, etc. Many homes worldwide use the same private IP ranges because they're isolated — your 192.168.1.10 is completely separate from someone else's 192.168.1.10. Only your router's public IP is visible to the outside world.

**Example:**

- Your laptop has private IP: `192.168.1.10`
- Your router has public IP: `203.0.113.45`
- When you browse the web, websites see your router's public IP, not your laptop's private IP

Your router does NAT to translate between them.

NAT (Network Address Translation) is how your router lets multiple devices share one public IP address. When your laptop (192.168.1.10) requests a webpage, the router remembers "this request came from laptop" and forwards it using its own public IP. When the response comes back, it translates it back and sends it to your laptop. It's like a receptionist at a company: outside callers only know the company's main number, but the receptionist knows which desk each call should reach internally.

This is why you can have 10 devices at home but only one public IP address.

**[IMAGE: Diagram showing a home network with multiple devices (laptop, phone, tablet) with private IPs (192.168.1.x) connected to a router with a public IP, connecting to the internet]**

---

### **Where Does Your IP Come From?**

**For your home/device:**

- Your ISP assigns it (often dynamically — it changes every few days)
- Sometimes you can pay for a static public IP

**For a website/server:**

- The hosting provider assigns a static IP
- Often hidden behind a load balancer or CDN, which has its own IPs

So when DNS tells your browser "[google.com](http://google.com) is at 142.250.80.46," that's Google's (or their CDN's) public IP address.

---

### **One Domain, Many IPs**

Here's a fun fact: **one domain can point to multiple IP addresses.**

Try this in your terminal:

```bash
nslookup google.com
```

You'll likely see multiple IP addresses returned. This is for:

**Load balancing**: Distribute traffic across multiple servers

**Geographic distribution**: Serve users from the nearest server

**Redundancy**: If one server fails, others keep working

Your browser might try them in sequence or pick one. The DNS response can even vary based on _where you are_ (this is called GeoDNS).

GeoDNS (Geographic DNS) returns different IP addresses based on where the request comes from. If you're in Tokyo, [google.com](http://google.com) might resolve to a Japanese data center. If you're in London, you get a UK data center. This makes websites faster (shorter physical distance) and more reliable (local servers stay up even if distant ones fail). It's invisible to users but critical for global services.

GeoDNS has a subtle limitation—it sees the DNS resolver's location (often your ISP's resolver, or a public one like Google's), not necessarily YOUR location. So if you're in Tokyo but using a US-based public DNS resolver, you might get routed to a US server.