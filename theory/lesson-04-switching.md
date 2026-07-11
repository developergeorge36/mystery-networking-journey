# Lesson 4: Switching

## Why This Lesson Matters

Lesson 3 covered how a single switch learns MAC addresses. This lesson builds on that to cover how switches are used in real networks: segmenting traffic with VLANs, connecting multiple switches with trunks, preventing loops with STP, and hardening ports with security features. This is core CCNA territory and the foundation for your VLAN lab.

---

## VLANs (Virtual LANs)

A **VLAN** logically segments a single physical switch (or group of switches) into multiple separate broadcast domains — without needing separate physical hardware for each group.

- Every VLAN is its own broadcast domain. Devices in different VLANs **cannot** talk to each other without a router (or Layer 3 switch) — this ties directly back to Lesson 3's point that only a router breaks up a broadcast domain.
- VLANs are identified by a **VLAN ID** (1–4094). VLAN 1 is the default on Cisco switches — best practice is to avoid using it for real traffic.
- Common real-world use: separating Sales, Engineering, Guest Wi-Fi, and IP phones onto different VLANs even though they may all plug into the same physical switches in a building.

**Why VLANs matter**: security (isolating traffic), performance (smaller broadcast domains = less unnecessary traffic), and organizational clarity (grouping by department/function instead of physical location).

---

## Access Ports vs Trunk Ports

- **Access port**: assigned to exactly **one** VLAN. This is where end devices (PCs, printers, phones) physically plug in. The switch strips any VLAN tagging before the frame reaches the device — the device has no awareness that VLANs exist.

- **Trunk port**: carries traffic for **multiple** VLANs over a single physical link, typically between switches, or between a switch and a router/firewall. Frames are tagged so the receiving end knows which VLAN each frame belongs to.

**Analogy**: think of a trunk port as a highway carrying several lanes (VLANs) of traffic at once, while an access port is a single-lane driveway dedicated to one house (one VLAN).

---

## 802.1Q Tagging & Native VLAN

**802.1Q** is the industry-standard protocol for VLAN tagging on trunk links.

- When a frame enters a trunk port, the switch inserts a small tag into the Ethernet frame header containing the VLAN ID — this is how the receiving switch knows which VLAN the frame belongs to.
- The tag is removed again before the frame reaches an access port/end device.

**Native VLAN**: on every trunk, one VLAN is designated as "native" — frames belonging to it are sent **untagged**. By default this is VLAN 1.

- Best practice: change the native VLAN away from VLAN 1 and make sure it **matches on both ends** of the trunk. A mismatched native VLAN is a classic real-world misconfiguration that causes VLAN leakage and security issues.

---

## Inter-VLAN Routing

Since VLANs are separate broadcast domains, something operating at Layer 3 is required for VLAN A to communicate with VLAN B.

Two common methods:

1. **Router-on-a-Stick**: a single physical router interface connects to a trunk port on the switch. The router's interface is divided into **sub-interfaces** — one per VLAN — each with its own IP address acting as that VLAN's default gateway. All inter-VLAN traffic passes through this one physical link.

2. **Layer 3 switch (SVI - Switched Virtual Interface)**: a switch capable of routing creates a virtual interface per VLAN directly on itself, removing the need for an external router altogether. This is the more common/scalable approach in real enterprise networks, though CCNA labs often start with router-on-a-stick since it's conceptually simpler.

**Why it matters**: This is one of the most commonly tested and most commonly used real-world configurations — expect to build this exact scenario in Packet Tracer soon.

---

## Spanning Tree Protocol (STP)

Redundant links between switches are good for reliability, but redundant **Layer 2** links create **loops** — and unlike Layer 3, Ethernet frames have no "hop count" or TTL to stop them from circulating forever. A loop causes a **broadcast storm**, which can bring down a network in seconds.

**STP** prevents this by:
1. Electing a **Root Bridge** — the switch all path calculations are based on, chosen by lowest Bridge ID (a combination of priority + MAC address).
2. Every other switch calculates the shortest path to the root and designates a **Root Port** (its best path toward the root).
3. On each network segment, one switch becomes the **Designated Port** (the one forwarding traffic for that segment).
4. Any redundant port that would create a loop is placed into a **Blocking** state — it stays physically connected but doesn't forward traffic, unless the primary path fails, at which point it activates automatically.

**Port states**: Blocking → Listening → Learning → Forwarding (classic STP; takes up to ~50 seconds to converge).

**RSTP (Rapid STP)**: the modern standard, converges in seconds instead of ~50 seconds, and is what most networks run today. CCNA expects you to know both concepts but focus on RSTP as current practice.

**Why it matters**: Without STP, plugging in a second cable between two switches "just to be safe" can crash an entire network almost instantly. This is a real mistake new techs make before understanding STP.

---

## EtherChannel / Port Aggregation (Concept-Level)

**EtherChannel** bundles multiple physical switch links into a single logical link — increasing bandwidth and providing redundancy without STP blocking any of the bundled ports (since STP treats the bundle as one link).

- Example: 2× 1Gbps physical links bundled into one logical 2Gbps EtherChannel.
- Configured using protocols like **PAgP** (Cisco proprietary) or **LACP** (industry standard).

At this stage, just understand *why* it exists: more bandwidth + redundancy between switches, without triggering STP to block one of the links.

---

## Port Security (Concept-Level)

A security feature that restricts which (and how many) MAC addresses are allowed on a given switch port.

- Prevents unauthorized devices from being plugged in and gaining network access.
- Can limit a port to a specific number of MACs (e.g., just 1, for a single PC), or to specific, manually defined MAC addresses.
- Violations can trigger actions like shutting the port down (`err-disabled` state), dropping the offending traffic, or just logging it — configurable depending on how strict you want to be.

**Why it matters**: A classic real-world scenario — someone plugs in an unauthorized switch or device into an open wall port, potentially creating a security hole or an accidental network loop. Port security is one of the basic hardening steps against this.

---

## Self-Check
1. Why can't two devices in different VLANs communicate without a router or Layer 3 switch?
2. What's the difference between an access port and a trunk port, and where would you use each?
3. What problem does STP solve, and what happens to a redundant link that would otherwise create a loop?
4. Name the two common methods for inter-VLAN routing, and briefly describe how each works.

- [ ] VLANs (purpose, tagging)
- [ ] Access ports vs trunk ports
- [ ] 802.1Q tagging & native VLAN
- [ ] Inter-VLAN routing (Router-on-a-Stick, L3 switching)
- [ ] STP/RSTP — root bridge election, port states, loop prevention
- [ ] EtherChannel/port aggregation (basic concept)
- [ ] Port security basics
