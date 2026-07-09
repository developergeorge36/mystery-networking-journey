# Lesson 1: Networking Fundamentals

## LAN vs WAN vs MAN vs PAN

These describe a network's **size/scope**.

- **LAN (Local Area Network)** — confined to one physical location: a home, office floor, school building. Owned/controlled by one organization. Fast, cheap, low latency (Ethernet/Wi-Fi).
  - *Example*: all PCs, printers, and APs inside a house or single office.

- **WAN (Wide Area Network)** — connects LANs across large distances (cities, countries, continents), usually via a service provider since you don't own the physical links. **The internet is the largest WAN in existence.**
  - *Example*: a company with offices in Accra and London connected via the internet or a leased line.

- **MAN (Metropolitan Area Network)** — between LAN and WAN in scale: spans a city or campus (e.g. multiple university buildings across town). Less commonly discussed today, but still shows up in textbooks/exams.

- **PAN (Personal Area Network)** — very short range, centered on one person's devices: Bluetooth headphones to a phone, a smartwatch syncing to a laptop.

**Why it matters**: The scope tells you what technology and toolset applies. LAN problems are usually cabling/switching issues. WAN problems are usually about the ISP link, routing, or the provider's network.

---

## Network Topologies

The **shape** a network is arranged in — physically (cable layout) or logically (data flow).

- **Bus** (legacy) — all devices share one cable. One break kills the whole network. Mostly obsolete, but foundational to understand why it was abandoned.

- **Ring** (legacy) — devices connected in a loop; data passes device to device. Mostly obsolete (Token Ring), though the concept survives in some fiber/WAN redundancy designs (SONET rings).

- **Star** — the dominant topology today. Every device connects to a central switch. One cable failing only affects that device.
  - This is what most home and office LANs look like.

- **Mesh** — every device connects to every/most others. Extremely resilient with many redundant paths, but expensive/complex to wire. Common in WAN backbones and critical infrastructure.

- **Hybrid** — real-world combination: star-wired at the access layer, with mesh-like redundancy between core switches/routers.

**Practical takeaway**: Your first Packet Tracer lab will be a star topology (PCs into a central switch). Multi-switch/router labs later add redundant links, which is where STP comes in to prevent loops.

---

## Client-Server vs Peer-to-Peer

Describes **how devices share resources**, not physical layout.

- **Client-Server**: dedicated **servers** offer resources (files, web pages, authentication, email); **clients** request them. Centralized, easier to secure and manage at scale — how virtually all business networks operate.
  - *Example*: your laptop (client) requesting a webpage from a company web server.

- **Peer-to-Peer (P2P)**: devices are equals — any device can request and provide resources. No central server. Simple/cheap for small setups, but doesn't scale well and is harder to secure.
  - *Example*: two laptops directly sharing files with no server; or torrenting.

**Why it matters**: Almost every professional network (Active Directory, DHCP, DNS, web servers) is client-server. P2P mostly survives in home file-sharing or specialized protocols.

---

## Bandwidth vs Throughput vs Latency vs Jitter

- **Bandwidth**: maximum theoretical capacity of a link — how much data *could* pass per second (Mbps/Gbps). The "advertised speed."

- **Throughput**: the *actual* data transferred per second in real conditions. Always ≤ bandwidth due to overhead, congestion, errors, retransmissions.
  - Analogy: bandwidth = number of highway lanes; throughput = cars actually getting through given traffic/accidents/tolls.

- **Latency**: time for data to travel from source to destination (ms). What `ping` measures (round-trip time). Affected by distance, hop count, processing delay.

- **Jitter**: variation in latency over time. Ping bouncing between 20ms, 80ms, 25ms, 90ms = high jitter. Even with fine average latency, high jitter wrecks real-time traffic (choppy VoIP, frozen video).

**Why it matters**: A link can have huge bandwidth but still feel slow for calls if latency/jitter are bad — this is why QoS exists, to prioritize latency-sensitive traffic.

---

## Self-Check
1. Why is the internet classified as a WAN and not a giant LAN?
2. Why did the industry move from bus/ring topologies to star?
3. Give one real example each of client-server and P2P from your own life.
4. Wi-Fi shows "300 Mbps" but a file transfer only hits 80 Mbps — which concept explains the gap?

- [x] LAN vs WAN vs MAN vs PAN
- [x] Network topologies (star, mesh, hybrid)
- [x] Client-server vs peer-to-peer
- [x] Bandwidth vs throughput vs latency vs jitter