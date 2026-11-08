---
type: synthesis
module: 2
question: "Describe the journey of a web request, from joining the network to the request leaving the laptop"
written_by: nasif
score:
last_recalled:
---
# First flow summary (Module 2, written before the checkpoint)

<!-- Nasif's summary, verbatim from raw/ (2026-11). Not edited. Corrections are listed separately below. -->

1. Device joins home network and gets basic network configuration from DHCP server run by router. It involves four messages (DORA):

    2.1. Discover: device searches for a DHCP server

    2.2. Offer: server proposes a configuration

2.3. Request: device requests for the proposed configuration

2.4. Acknowledge: server confirms the lease

The configuration generally includes:

1. An IP address

2. A subnet mask

3. A default gateway (IP address of the router)

4. DNS resolver address

  

5. Client makes a HTTP request to server

6. DNS resolution happens

7. 3-way TCP handshake happens

8. TLS handshake happens

9. Browser prepares the HTTP message and hands it over to OS TLS

10. TLS encrypts the message and hands over to OS TCP

11. TCP slices the byte stream into segments, adds TCP header, sequence number, checksum, flags and hands it over to IP

12. IP adds IP header, hands over the IP datagram to the Data Link Layer

  

Device joins network -> searches for a DCHP server -> DORA process happens (discovery, offer, request, acknowledgement) -> Device receives IP address, subnet mask, default gateway IP address, DNS resolver IP address

  

DNS -> Browser cache -> OS cache -> DNS Resolver -> Root DNS server -> TLD server -> Authoritative Name server


All the topics and concepts (likely atoms):
- Internet
- Host/End System
- Packet
- Segment
- Route/Path through the network
- Switch
- Router
- Communication Link
- ISP
- Protocol
- TCP
- UDP
- IP
- IOT
- Store & Forward Transmission Delay
- Queueing Delay
- Nodal Processing Delay
- Propagation Delay
- Packet Loss
- Routing Protocol
- Forwarding Table
- Circuit Switching
- Packet Switching
- Throughput
- Bandwidth
- Latency
- The OSI model
- 5 layers of network
- HTTP
- HTTPS
- TLS
- SSL
- Message
- Packet
- Segment
- Datagram
- Frame
- TTL
- DNS
- Socket
-

---

## Corrections (added by Claude, 2026-11-08)

The DHCP steps, the four configuration items and the DNS chain are correct. Three fixes and one gap:

1. **"hands it over to OS TLS … TLS encrypts the message and hands over to OS TCP."** TLS isn't in the OS. It's a library inside the browser (Chrome: BoringSSL; Firefox: NSS). The browser encrypts, then hands encrypted bytes to the OS's TCP through a socket. See [[m02-12-tls]].
2. **"5. Client makes a HTTP request to server" before "6. DNS resolution".** The user *starts* the request first, but nothing is sent until DNS, the TCP handshake and the TLS handshake are done. Order on the wire: DNS → TCP → TLS → HTTP.
3. **"TCP slices the byte stream into segments, adds TCP header, sequence number, checksum, flags".** Correct. Add: ports, the acknowledgment number and the receive window are in the header too. See [[m02-11-tcp]].
4. **The summary stops at "hands over the IP datagram to the Data Link Layer."** Missing: the subnet-mask decision, ARP for the gateway's MAC, framing, NAT at the router (twice if your ISP uses CGNAT), forwarding hop by hop, the server side, and the response. That's the Unit 5 cold trace.
