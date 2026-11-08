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