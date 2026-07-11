# Lesson 3: Physical & Data Link Layer (L1/L2)

## Why This Lesson Matters

Layers 1 and 2 are where networking gets physical and tangible — cables, connectors, MAC addresses, switches. Almost every "why won't this device connect" problem starts here. This is also where you build the deepest intuition for how switches actually behave, which is foundational for everything after (VLANs, STP, troubleshooting).

---

## Cable Types

### Copper (Twisted Pair)
- **Cat5e**: up to 1 Gbps, common in older/budget installs
- **Cat6**: up to 10 Gbps (shorter distances), the current practical standard
- **Cat6a/Cat7+**: higher speeds/distances, used in newer enterprise builds
- Uses **RJ45 connectors**

### Fiber Optic
- **Single-mode fiber (SMF)**: one light path, long distances (kilometers), used for WAN links and long backbone runs — yellow jacket by convention
- **Multi-mode fiber (MMF)**: multiple light paths, shorter distances (hundreds of meters), used for in-building/campus backbone — orange or aqua jacket by convention
- Immune to electromagnetic interference (EMI) — a major advantage over copper in noisy environments (near heavy machinery, power lines)

**Why it matters**: Choosing the wrong cable for the distance/environment is a real-world design mistake. Fiber costs more but solves distance and interference problems copper can't.

---

## Cable Wiring Types

- **Straight-through**: connects *different* device types — PC-to-switch, switch-to-router. Both ends wired identically.
- **Crossover**: connects *same* device types — switch-to-switch, PC-to-PC, router-to-router. Wiring is crossed so transmit pins line up with receive pins.
- **Console (rollover) cable**: connects your PC to a device's console port for direct CLI configuration — not for data traffic, purely management access. In real life, often a USB-to-console adapter today.
- **Serial cable**: used for WAN links between routers (in Packet Tracer, used for router-to-router "long distance" simulated links). One side is **DCE** (sets the clock rate), the other is **DTE**.

**Modern note**: Most current switches/NICs support **Auto-MDI-X**, meaning they auto-detect and adjust, so straight-through vs crossover often doesn't matter in practice anymore. Still know the theory — it's tested and it explains *why* the auto-detection feature exists.

---

## MAC Addresses

A **MAC address** (Media Access Control) is a 48-bit address burned into every network interface card (NIC) — it's the device's physical identity at Layer 2.

- Written as 12 hex digits: `00:1A:2B:3C:4D:5E` or `001A.2B3C.4D5E` (Cisco format)
- Split into two halves:
  - First 24 bits = **OUI** (Organizationally Unique Identifier) — identifies the manufacturer (Cisco, Dell, Apple, etc.)
  - Last 24 bits = unique identifier assigned by that manufacturer
- MAC addresses are **flat** (not hierarchical like IP) and **local to the LAN only** — they don't change as a packet crosses routers, but they're rewritten frame-by-frame at each Layer 2 hop.

---

## Ethernet Frames

The **frame** is the Layer 2 data unit — it wraps a packet with Layer 2 addressing info before it hits the wire.

Basic Ethernet frame structure:
```
[Preamble] [Destination MAC] [Source MAC] [EtherType/Length] [Data/Payload] [FCS]
```
- **Destination/Source MAC**: who it's from, who it's going to (locally)
- **EtherType**: tells the receiving device what's inside (e.g., IPv4, IPv6, ARP)
- **FCS (Frame Check Sequence)**: a checksum used to detect transmission errors — if the frame arrives corrupted, it's silently dropped

---

## How Switches Learn (MAC Address Table)

One of the most important behaviors to understand deeply — it explains almost everything a switch does.

1. A frame arrives on a switch port.
2. The switch looks at the **source MAC address** and records: "this MAC lives on this port" — building its **MAC address table** (also called CAM table).
3. The switch looks at the **destination MAC address**:
   - If it's already in the table → sends the frame **only** out that specific port (this is why switches are efficient — no unnecessary flooding).
   - If it's **not** in the table yet → **floods** the frame out every port except the one it arrived on (this is how it "discovers" where things are).
4. Once the destination device replies, the switch learns its MAC too, and future traffic between those two devices is sent directly, port-to-port.

This table has a timeout (usually 5 minutes on Cisco switches by default) — if a device goes quiet, its entry eventually ages out and gets removed.

**Command to see this live**: `show mac address-table`

---

## Collision Domains vs Broadcast Domains

- **Collision Domain**: a segment where two devices transmitting at the same time can cause a collision (garbled data).
  - Old hubs = one giant shared collision domain (everyone competes for the wire).
  - Modern switches = **every port is its own collision domain** — this is why switches essentially eliminated collisions in wired LANs.

- **Broadcast Domain**: the set of devices that receive a broadcast frame (destination `FFFF.FFFF.FFFF`) sent by any device in the group.
  - A switch does **NOT** break up broadcast domains — a broadcast sent by any device reaches every device connected to that switch (or trunked to other switches), by default.
  - Only a **router** (or a VLAN boundary) breaks up a broadcast domain.

**Why it matters**: This is *the* reason VLANs exist — to break a single physical switch into multiple, smaller broadcast domains without needing separate physical switches or routers for each group.

---

## Duplex & Speed

- **Half-duplex**: device can send OR receive at a time, not both simultaneously (like an old walkie-talkie). Rare today, mostly a legacy/hub-era concept.
- **Full-duplex**: device can send AND receive simultaneously (like a phone call). Standard on all modern switch/NIC connections.
- **Duplex mismatch**: one side set to full, the other to half — causes intermittent errors, slow performance, and is a classic real-world troubleshooting scenario. Usually caused by manually forcing settings instead of leaving **auto-negotiation** on.

---

## ARP (Address Resolution Protocol)

ARP is the bridge between Layer 3 (IP) and Layer 2 (MAC) — it answers the question: *"I know this device's IP address, but what's its MAC address?"*

**The process:**
1. Device A wants to send data to `192.168.1.11` but only knows the IP, not the MAC.
2. Device A sends an **ARP Request** — a broadcast: *"Who has 192.168.1.11? Tell me your MAC."*
3. Every device on the LAN receives it, but only the device with that IP responds.
4. That device sends an **ARP Reply** — unicast, directly back to Device A — containing its MAC address.
5. Device A now caches this in its **ARP table** and can build the Ethernet frame properly (source and destination MAC now known).

**Command to see this**: `arp -a` on a PC shows the local ARP cache — a mapping of known IP-to-MAC pairs.

**Why it matters**: Every single time a device communicates with a new device on its local network, ARP runs first (unless the mapping is already cached). It's invisible in daily use but constant — and it's a classic first step when troubleshooting "device can't reach another device on the same subnet."

---

## Self-Check
1. Explain, step by step, how a switch builds its MAC address table.
2. What's the difference between a collision domain and a broadcast domain — and which device breaks up each one?
3. Why does ARP use a broadcast for the request but a unicast for the reply?
4. You plug in a device and its status light flickers red/amber instead of solid green — what layer are you troubleshooting, and what would you check first?

- [x] Cable types: copper (Cat5e/6) vs fiber (single-mode/multi-mode)
- [x] Straight-through vs crossover vs console vs serial cables
- [x] MAC addresses (structure, OUI)
- [x] Ethernet frames & framing
- [x] Switch MAC address table / learning & flooding
- [x] Collision domains vs broadcast domains
- [x] Duplex (half vs full) and speed mismatches
- [x] ARP (Address Resolution Protocol)
