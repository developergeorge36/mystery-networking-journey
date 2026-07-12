# Lesson 7: Transport Layer

## Why This Lesson Matters

Layer 4 is where "reliable delivery" actually gets decided. Every application you've ever used — web browsing, video calls, file transfers, DNS lookups — relies on either TCP or UDP to move its data, and the choice between them shapes how that application behaves. This lesson also introduces **ports**, which you'll use constantly in ACLs, firewalls, and NAT configuration.

---

## TCP vs UDP — The Core Distinction

Both are Layer 4 protocols responsible for getting data from a source application to a destination application, but they take very different approaches.

### TCP (Transmission Control Protocol) — Reliable
- **Connection-oriented**: a connection is formally established (via the three-way handshake) before any data is sent
- **Reliable delivery**: every segment is acknowledged; if something is lost, it's retransmitted
- **Ordered delivery**: segments are sequenced and reassembled in the correct order at the destination, even if they arrive out of order
- **Flow control**: the receiver can tell the sender to slow down if it's being overwhelmed
- **Overhead**: all of this reliability costs speed — more overhead, more back-and-forth communication

**Used for**: anything where accuracy matters more than speed — web browsing (HTTP/HTTPS), email (SMTP), file transfers (FTP), remote access (SSH)

### UDP (User Datagram Protocol) — Fast, "Best Effort"
- **Connectionless**: no handshake, no formal setup — data is just sent
- **No delivery guarantee**: no acknowledgments, no retransmission if something is lost
- **No ordering guarantee**: segments (called datagrams in UDP) can arrive out of order and UDP won't fix that
- **Minimal overhead**: much faster, much lighter than TCP

**Used for**: anything where speed matters more than perfect accuracy — video/voice calls (VoIP), live streaming, online gaming, DNS lookups

**Simple way to remember**: TCP is like a phone call with a confirmation for every sentence spoken ("did you get that? okay, continuing..."). UDP is like shouting information across a room — fast, but if someone doesn't hear part of it, no one's going back to repeat it.

---

## The Three-Way Handshake (TCP Only)

Before any actual data flows over TCP, the two devices negotiate and confirm the connection using three messages:

1. **SYN** — Device A sends a SYN ("synchronize") packet to Device B: *"I want to start a connection, here's my starting sequence number."*
2. **SYN-ACK** — Device B responds with SYN-ACK: *"Got it, I acknowledge your request, and here's my starting sequence number too."*
3. **ACK** — Device A responds with ACK: *"Acknowledged, connection established."*

After this exchange, both sides are synchronized and ready to reliably exchange data.

**Why it matters practically**: this handshake is visible in every packet capture involving TCP traffic (e.g., in Wireshark) and is a foundational concept for understanding firewall behavior — a stateful firewall tracks these handshakes to know which sessions are legitimately established.

**Connection teardown**: TCP also formally closes connections (using FIN/ACK exchanges) rather than just stopping — another reliability feature UDP doesn't have.

---

## Port Numbers

A **port number** identifies *which application or service* on a device the traffic belongs to — this is what lets a single device run many network conversations simultaneously (web browsing, email, and a game all running at once) without confusion.

Ports range from 0–65535, divided into three categories:
- **Well-known ports (0–1023)**: reserved for standard, widely used services
- **Registered ports (1024–49151)**: used by specific applications, registered but not as tightly controlled
- **Dynamic/private ports (49152–65535)**: temporary, assigned on-the-fly for client-side connections

### Well-Known Ports Worth Memorizing

| Port | Protocol | Service |
|---|---|---|
| 20/21 | TCP | FTP (data/control) |
| 22 | TCP | SSH |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP (email sending) |
| 53 | TCP/UDP | DNS |
| 67/68 | UDP | DHCP (server/client) |
| 80 | TCP | HTTP |
| 110 | TCP | POP3 |
| 143 | TCP | IMAP |
| 443 | TCP | HTTPS |
| 3389 | TCP | RDP (Remote Desktop) |

**Why it matters practically**: when you write an ACL or configure a firewall rule, you're very often filtering based on these exact port numbers (e.g., "allow TCP port 443 outbound, block everything else"). Memorizing these makes reading and writing rules dramatically faster.

---

## Sockets

A **socket** is the combination of an **IP address + port number**, uniquely identifying one specific communication endpoint.

Example: `192.168.1.10:443` is a socket — it identifies exactly which device (by IP) and which service on that device (by port) is involved in a conversation.

A full TCP connection is actually defined by **four** values together (called a socket pair): source IP, source port, destination IP, destination port. This is how a device can have multiple simultaneous connections to the same destination IP and port (e.g., multiple browser tabs open to the same website) — the source port differs for each one, keeping every connection distinct.

---

## Self-Check
1. Explain the three-way handshake, step by step, in your own words.
2. Give two real-world examples of when you'd choose UDP over TCP, and explain why.
3. What port number is used for HTTPS? For DNS? For SSH?
4. What four values make up a full TCP connection's socket pair, and why does a device need all four to track multiple simultaneous connections to the same server?

- [ ] TCP vs UDP — reliability, use cases
- [ ] Three-way handshake
- [ ] Port numbers — well-known ports
- [ ] Sockets (IP + port combo)
