# Lesson 6: Routing

## Why This Lesson Matters

Everything up to now has lived inside a single LAN/broadcast domain. Routing is what connects separate networks together — it's the reason the internet works at all, and it's the core skill that turns you from someone who understands switching into someone who understands how traffic actually gets from one network to another.

---

## What a Router Actually Does

A **router** operates at Layer 3. Its core job, every single time a packet arrives, is:

1. Look at the packet's **destination IP address**
2. Check its **routing table** for the best matching route
3. Forward the packet out the correct interface toward that destination
4. If no matching route exists (and no default route is configured) → the packet is dropped, and the router typically sends back an ICMP "destination unreachable" message

This is fundamentally different from switching: a switch forwards based on MAC address within one broadcast domain; a router forwards based on IP address, connecting *separate* broadcast domains/networks together.

---

## The Routing Table

Every router maintains a routing table — think of it as the router's map of "how do I get to this network." Run `show ip route` on a Cisco device to view it.

Each entry typically includes:
- **Destination network** (with its subnet mask/CIDR)
- **Next hop** — the IP address of the next router to send the packet to (or "directly connected" if it's on a locally attached network)
- **Outgoing interface** — which physical/logical interface to send it out of
- **Source of the route** — how the router learned it (directly connected, static, or a specific dynamic routing protocol)
- **Metric/cost** — how "good" this path is, used to pick between multiple routes to the same destination

**Longest prefix match rule**: if a router has multiple matching routes to the same destination (e.g., both a /24 and a /32 route to the same address range), it always picks the **most specific** match — the one with the longest subnet mask — regardless of other factors.

---

## Static Routing

A **static route** is one you type in manually, telling the router exactly how to reach a specific network.

```
ip route <destination-network> <subnet-mask> <next-hop-or-exit-interface>
```

Example: `ip route 192.168.2.0 255.255.255.0 10.0.0.2`

**Pros**: simple, predictable, no extra protocol overhead, more secure (no route advertisements to intercept)
**Cons**: doesn't adapt automatically — if a link fails, static routes don't reroute themselves; you'd need to manually intervene or have a pre-configured backup route

**When it's used in the real world**: small networks, stub networks (a network with only one path in/out), or specific scenarios like default routes even in larger networks.

### Default Route
A special static route — `0.0.0.0 0.0.0.0` — that matches *any* destination not covered by a more specific route. This is typically how a router sends traffic destined for "the internet" toward its ISP-facing interface, without needing a route for every possible destination on Earth.

---

## Dynamic Routing

Dynamic routing protocols let routers **automatically discover and share routes** with each other, adapting to network changes without manual intervention.

### OSPF (Open Shortest Path First) — the CCNA-focus protocol

OSPF is a **link-state** routing protocol:

1. Routers running OSPF discover each other and become **neighbors** by exchanging "Hello" packets on directly connected links.
2. Once neighbors, they exchange full information about their directly connected networks — building a complete, shared picture (the "link-state database") of the entire topology within that OSPF area.
3. Each router independently runs the **Dijkstra Shortest Path First algorithm** against that database to calculate the best path to every known network.
4. The best paths get installed into the routing table.

**Cost** is OSPF's metric — calculated based on interface bandwidth (higher bandwidth = lower cost = more preferred). Lower total cost to a destination wins.

**Areas**: OSPF networks are divided into areas (Area 0 is the "backbone," required in any multi-area design) to keep link-state databases smaller and more manageable as networks grow. CCNA typically focuses on **single-area OSPF** — everything in Area 0.

**Why OSPF over static routing at scale**: in a network with many routers and links, manually maintaining static routes becomes unmanageable, and any link failure requires immediate manual fixes. OSPF adapts automatically — if a link goes down, routers recalculate and reroute within seconds.

### Other protocols worth knowing (conceptually, not deep-dive at this stage)
- **EIGRP**: Cisco-proprietary (now partially open), a hybrid distance-vector/link-state protocol, historically popular in Cisco-only environments
- **BGP** (Border Gateway Protocol): the protocol that runs the actual internet backbone, used between ISPs and very large organizations — not used inside a typical LAN, but essential to know conceptually since it's what makes the global internet's routing work

---

## Administrative Distance & Metric — Don't Confuse These

- **Administrative Distance (AD)**: a trust rating used when a router learns the *same* destination network from **different sources** (e.g., a static route AND OSPF both claim to know how to reach `192.168.5.0/24`). Lower AD wins.

| Source | Default AD |
|---|---|
| Directly connected | 0 |
| Static route | 1 |
| EIGRP | 90 |
| OSPF | 110 |
| RIP | 120 |

- **Metric**: used to compare *multiple paths within the same protocol* to determine the best route (e.g., OSPF cost, EIGRP's composite metric). Only compared when AD is already equal.

**Simple way to remember**: AD picks the winning *protocol/source*. Metric picks the winning *path*, once you're already committed to one protocol.

---

## Default Gateway (First-Hop Concept)

Every device on a LAN needs a **default gateway** — the IP address of the router interface it sends traffic to whenever the destination isn't on its own local subnet.

- If a PC's IP and destination IP are in the same subnet → it ARPs for the destination directly and sends the frame locally (no router involved).
- If they're in different subnets → the PC sends the frame to its default gateway's MAC address, and the router takes it from there.

This directly connects back to Lesson 3's ARP process and Lesson 5's subnetting — the PC determines "same subnet or not" using its own subnet mask before deciding whether to involve the gateway at all.

---

## Self-Check
1. Walk through, step by step, what a router does when a packet arrives.
2. What's the difference between Administrative Distance and Metric? Give an example of when each would be used.
3. Why does a router prefer a more specific route (longer prefix) over a less specific one, even if the less specific one has a better metric?
4. Explain, in your own words, how OSPF routers become neighbors and build their routing tables.

- [ ] Routing tables — how routers pick a path
- [ ] Static routing (incl. default route)
- [ ] Dynamic routing — OSPF fundamentals (neighbors, cost, areas)
- [ ] Administrative distance & metric
- [ ] First-hop concepts: default gateway
