# Lesson 11: WAN & Internet Connectivity

## Why This Lesson Matters

Everything so far has largely focused on how traffic moves within a LAN. This lesson zooms out to how separate LANs actually connect to each other across distance — the WAN link types you'll encounter, and the diagnostic tools (ping, traceroute) you'll use constantly to test and troubleshoot that connectivity.

---

## WAN Link Types

Unlike a LAN (which you fully own and control), a WAN link almost always involves a service provider, since you don't own the physical infrastructure spanning long distances.

- **Leased Line**: a dedicated, private point-to-point connection between two locations, leased from a provider. Consistent, predictable bandwidth, but expensive relative to shared alternatives — historically common for connecting branch offices to headquarters.

- **MPLS (Multiprotocol Label Switching)** *(conceptual awareness)*: a provider-run technology that efficiently forwards traffic across a WAN using labels rather than full routing lookups at every hop. Commonly used by businesses to connect multiple sites through a provider's network with predictable performance — you don't need deep MPLS configuration knowledge at CCNA level, just awareness that it exists and what problem it solves (efficient, provider-managed multi-site connectivity).

- **Broadband**: typical internet connections used by homes and small businesses — cable, DSL, fiber-to-the-home. Shared bandwidth (to varying degrees depending on the technology), cheaper than a leased line, and increasingly fast enough to serve as a legitimate business WAN connection, sometimes even paired with VPN site-to-site tunnels (Lesson 9) as a cost-effective alternative to a traditional leased line.

**Why it matters practically**: choosing a WAN link type is a real business/design tradeoff — cost vs reliability vs bandwidth guarantees. A company might use a leased line as a primary connection and broadband as a cheaper backup path.

---

## ICMP, Ping, and Traceroute

**ICMP (Internet Control Message Protocol)** operates at Layer 3 and is used for diagnostic and error-reporting purposes — it's not used to carry actual application data, but to communicate *about* the network itself.

### Ping
`ping` sends **ICMP Echo Request** messages to a destination and listens for **ICMP Echo Reply** messages back.

- Confirms basic reachability between two devices
- Reports round-trip time (latency, from Lesson 1) and packet loss
- A failed ping doesn't always mean "the destination is down" — it could mean ICMP is being blocked somewhere along the path (many firewalls block ICMP deliberately), so a lack of ping reply is a clue, not a definitive answer

### Traceroute (tracert on Windows)
`traceroute` reveals the **path** — every router hop — a packet takes to reach a destination, and the round-trip time to each hop along the way.

**How it actually works**: traceroute sends a series of packets with a gradually increasing **TTL (Time To Live)** value, starting at 1.
1. The first packet has TTL=1. The first router along the path decrements TTL to 0, drops the packet, and sends back an ICMP "Time Exceeded" message — revealing that router's address as hop 1.
2. The next packet has TTL=2, making it one hop further before expiring — revealing hop 2.
3. This continues, incrementing TTL each round, until a packet finally reaches the destination.

**Why it matters practically**: if ping to a destination fails, traceroute helps pinpoint *where* along the path the traffic is being dropped — is it failing at the very first hop (a local/LAN issue), somewhere in the middle (an ISP or WAN issue), or right at the final hop (a destination-side issue, like a firewall blocking ICMP but the rest of the path being fine)? This maps directly back to the bottom-up/divide-and-conquer troubleshooting methodology from Lesson 2.

---

## QoS (Quality of Service) — Concept-Level

Not all traffic is equally sensitive to delay. Recall Lesson 1's distinction between latency and jitter — a file download can tolerate some delay just fine, but a VoIP call or video conference becomes unusable with even small amounts of jitter.

**QoS** is a set of techniques that let a network **prioritize** certain types of traffic over others when bandwidth is limited or congested, rather than treating everything equally on a first-come-first-served basis.

- Traffic can be **classified** (identified and tagged by type — voice, video, bulk data, etc.) and then **prioritized** accordingly, so time-sensitive traffic (like a VoIP call) gets forwarded ahead of less time-sensitive traffic (like a large file download) when a link is under pressure.
- Without QoS, a large file transfer happening at the same time as a video call can starve the call of bandwidth, causing exactly the kind of jitter/latency spikes that make calls choppy or drop entirely.

At this stage, just understand *why* QoS exists and the problem it solves — deep QoS configuration (marking, queuing, policing) is a more advanced topic typically built on top of solid routing/switching fundamentals.

---

## Self-Check
1. What's the practical tradeoff between a leased line and broadband as a WAN connection type?
2. Why doesn't a failed ping always mean the destination device is down?
3. Explain, step by step, how traceroute uses TTL to map out the path to a destination.
4. Give a real-world example of why QoS matters — what happens without it when a video call and a large download compete for the same limited bandwidth?

- [ ] WAN vs LAN link types (leased line, MPLS conceptually, broadband)
- [ ] ICMP & ping/traceroute (how they work, not just how to run them)
- [ ] Basic QoS concept (why it exists, not deep config)
