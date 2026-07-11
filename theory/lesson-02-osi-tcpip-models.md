# Lesson 2: OSI & TCP/IP Models

## Why This Lesson Matters More Than It Seems

Every troubleshooting conversation, every certification exam question, every "why isn't this working" moment in your career will map back to these models. They're not just theory to memorize — they're the mental framework used to isolate problems for the rest of your career.

---

## The OSI Model (7 Layers)

OSI = **O**pen **S**ystems **I**nterconnection. A conceptual model that breaks networking into 7 distinct layers, each with a specific job. Data gets built up (or torn down) layer by layer as it moves through the network.

**Mnemonic (top to bottom)**: "**A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing"

| # | Layer | Job | Real-world examples | PDU (data unit) |
|---|---|---|---|---|
| 7 | **Application** | Interface between the network and the actual software/user | HTTP, HTTPS, FTP, SMTP, DNS | Data |
| 6 | **Presentation** | Formats, encrypts, compresses data so both ends understand it | SSL/TLS, JPEG, ASCII, encryption | Data |
| 5 | **Session** | Opens, manages, and closes communication sessions between devices | NetBIOS, RPC, session tokens | Data |
| 4 | **Transport** | End-to-end delivery, reliability, flow control | TCP, UDP | Segment |
| 3 | **Network** | Logical addressing and routing between networks | IP, ICMP, routers | Packet |
| 2 | **Data Link** | Physical addressing (MAC), framing, switches | Ethernet, MAC addresses, switches | Frame |
| 1 | **Physical** | Raw bits over physical medium | Cables, connectors, radio signals, hubs | Bits |

### The layers that matter most day-to-day
- **Layer 1 (Physical)** — "is the cable plugged in / is the light green?"
- **Layer 2 (Data Link)** — switches, MAC addresses, VLANs
- **Layer 3 (Network)** — routers, IP addressing, routing
- **Layer 4 (Transport)** — TCP/UDP, ports, firewalls filtering by port
- Layers 5–7 matter more to developers/application teams, but still needed conceptually for the exam and full-picture troubleshooting.

---

## Encapsulation & De-encapsulation

This is *how* data actually travels through the OSI layers — one of the most tested CCNA concepts.

**Encapsulation** (sending side): as data moves DOWN the stack, each layer wraps it with its own header (and sometimes trailer) info.

```
Application data
   ↓ (Transport layer adds a header)
Segment  [TCP/UDP header | Data]
   ↓ (Network layer adds a header)
Packet   [IP header | TCP header | Data]
   ↓ (Data Link layer adds header + trailer)
Frame    [MAC header | IP header | TCP header | Data | FCS trailer]
   ↓ (Physical layer converts to signal)
Bits     [0101001011...]
```

**De-encapsulation** (receiving side): the exact reverse — each layer strips off its corresponding header as data moves UP the stack, until the original application data is delivered.

**Why it matters practically**: Packet analyzers (like Wireshark) show exactly these headers stacked on top of each other. Understanding encapsulation is what makes a packet capture readable — knowing exactly what's happening at each layer.

---

## TCP/IP Model (the 4-layer practical version)

OSI is the theoretical/teaching model. **TCP/IP** is what's actually implemented in real-world networking (including the internet). CCNA references both, so know how they map:

| TCP/IP Layer | Maps to OSI Layers | What it covers |
|---|---|---|
| **Application** | 5, 6, 7 | All app-facing protocols (HTTP, DNS, FTP, etc.) combined into one layer |
| **Transport** | 4 | TCP/UDP |
| **Internet** | 3 | IP addressing and routing |
| **Network Access** (aka Link) | 1, 2 | Physical + Data Link combined |

**Why two models exist**: OSI is more granular and better for teaching/troubleshooting language ("that's a Layer 2 issue"). TCP/IP is what protocols are actually built around — the internet runs on the TCP/IP suite, not a strict OSI implementation.

---

## PDU Names — Know These Cold

**Data → Segment (L4) → Packet (L3) → Frame (L2) → Bits (L1)**

Quick trick: "the switch drops the **frame**" = Layer 2 problem. "The router can't route the **packet**" = Layer 3 problem. This vocabulary precision is what separates people who sound like they know networking from people who actually do.

---

## Applying the Model: Troubleshooting Logic

This is the entire point of learning OSI — a systematic way to isolate problems instead of guessing.

**Bottom-up approach** (most common for hands-on/physical issues):
1. L1 — Is the cable connected? Is the port light green?
2. L2 — Does the switch see the MAC address? Is the VLAN correct?
3. L3 — Is the IP address/subnet mask/default gateway correct? Can you ping the gateway?
4. L4 — Is the right port open? Is a firewall blocking it?
5. L7 — Is the application itself working (e.g., is the web server actually running)?

**Top-down approach** (common when suspecting an application issue): start at L7 and work down — useful when many users can reach *some* things but not a specific app/service.

**Divide-and-conquer**: jump to a middle layer (commonly L3 — "can you ping the gateway?") and branch up or down depending on the result. Usually the fastest real-world method once you have experience.

---

## Self-Check
1. Recite all 7 OSI layers in order, top to bottom, without looking.
2. What's the PDU called at Layer 3? At Layer 2?
3. A user says "the website won't load, but I can ping the server's IP." Using bottom-up logic, which layer would you suspect first, and why?
4. Why does the internet run on TCP/IP rather than a strict OSI implementation?

- [x] OSI Model — all 7 layers, in order, both directions
- [x] TCP/IP Model — 4 layers, mapping to OSI
- [x] Encapsulation/de-encapsulation
- [x] PDU names at each layer
