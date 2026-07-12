# Lesson 9: Security Fundamentals

## Why This Lesson Matters

Every network you'll ever manage is a target. Security isn't a separate specialty bolted onto networking — it's baked into how you design VLANs, write ACLs, configure switches, and set up remote access. This lesson covers the CCNA-level security concepts every network engineer is expected to know, regardless of whether "security" ends up being your specialty.

---

## ACLs (Access Control Lists) — Full Depth

You've seen ACLs referenced already — this is the full picture.

An **ACL** is a list of rules configured on a router (or Layer 3 switch) that permits or denies traffic based on criteria like source/destination IP, protocol, and port number.

### Standard ACLs
- Filter based on **source IP address only**
- Simple but blunt — you can't distinguish between different types of traffic from the same source
- **Numbered range**: 1–99, 1300–1999
- Best practice placement: **as close to the destination as possible** — because standard ACLs can't see the destination, placing them too early could block traffic meant for other destinations too

### Extended ACLs
- Filter based on **source IP, destination IP, protocol (TCP/UDP/ICMP), and port number**
- Much more granular — this is what's used in almost all real-world scenarios
- **Numbered range**: 100–199, 2000–2699
- Best practice placement: **as close to the source as possible** — since they can already identify exactly what the traffic is, there's no reason to let it travel further into the network before blocking it

### Processing Rules (critical, frequently tested)
- ACL entries are processed **top-down**, in order
- The **first match wins** — once a rule matches, processing stops; remaining rules are never evaluated
- Every ACL has an **implicit deny** at the very end — if traffic doesn't match any explicit rule, it's dropped by default
- This means a poorly ordered ACL (e.g., a broad "deny" rule placed before a specific "permit" rule) can accidentally block traffic you meant to allow — order matters enormously

**Why it matters practically**: ACLs are one of the most hands-on, frequently-configured features in real network engineering work — used for basic firewalling, restricting management access, and segmenting traffic between VLANs/subnets.

---

## AAA (Authentication, Authorization, Accounting)

A framework for controlling and tracking access to network devices — the conceptual foundation behind virtually every login system you'll manage.

- **Authentication**: *"Who are you?"* — verifying identity, typically via username/password, but can include stronger methods (certificates, multi-factor authentication)
- **Authorization**: *"What are you allowed to do?"* — once authenticated, what level of access/privilege does this identity get (e.g., read-only vs full config access)
- **Accounting**: *"What did you actually do?"* — logging actions taken, for auditing and troubleshooting purposes

In real enterprise environments, AAA is often centralized using protocols like **RADIUS** or **TACACS+**, so admins don't need separate local accounts on every single device — one central server handles authentication for the entire network.

---

## Port Security (Revisited From Lesson 4, Now With Detail)

Restricts which (and how many) MAC addresses are allowed to connect on a specific switch port.

- **Static**: manually specify allowed MAC address(es) on a port
- **Dynamic**: switch learns and locks in the first MAC(s) it sees, up to a configured maximum
- **Sticky**: dynamically learned MACs are saved into the running configuration, persisting across reboots (a practical middle ground between static and dynamic)

**Violation actions** (what happens when an unauthorized MAC is detected):
- **Protect**: silently drops offending traffic, no logging
- **Restrict**: drops offending traffic AND logs/counts the violation
- **Shutdown** (default): puts the port into an **err-disabled** state, requiring manual (or auto-recovery) intervention to bring it back up

**Why it matters**: prevents unauthorized devices — or an employee plugging in a personal switch/router, accidentally creating a loop or security hole — from gaining network access through an open port.

---

## DHCP Snooping (Concept-Level)

A security feature that protects against **rogue DHCP servers** — unauthorized or malicious devices handing out incorrect IP configuration to unsuspecting clients (a common attack vector called a "DHCP spoofing" attack, and also just a common accidental misconfiguration when someone plugs in a random router with DHCP still enabled).

- Switch ports are classified as **trusted** (connected to your legitimate DHCP server) or **untrusted** (connected to end-user devices)
- DHCP server responses (offers, acknowledgments) are only permitted from **trusted** ports — if an untrusted port tries to send DHCP server-type traffic, it's blocked

**Why it matters**: this is a very real, very common real-world problem — someone plugging in a personal wireless router with DHCP still enabled can silently break an entire office's network by handing out conflicting IP addresses. DHCP snooping is the standard defense.

---

## VPN Basics (Conceptual)

A **VPN (Virtual Private Network)** creates an encrypted tunnel over an untrusted network (usually the public internet), so traffic inside that tunnel is protected from eavesdropping and tampering.

- **Site-to-site VPN**: connects two entire networks together permanently (e.g., a company's headquarters and a branch office), so devices on either side can communicate as if they were on the same LAN, over an encrypted tunnel across the internet
- **Remote-access VPN**: an individual user (e.g., someone working from home) connects their device into the company network securely, typically using client software

At this stage, know the conceptual difference and purpose — deeper VPN configuration (IPSec, tunneling protocols) is typically beyond CCNA's introductory scope but good to be aware of.

---

## Firewall Basics (Conceptual)

A **firewall** is a device (or feature on a router) that inspects and filters traffic based on security rules — think of it as an ACL's more powerful, purpose-built cousin.

- **Stateless firewall**: examines each packet independently, with no memory of previous packets — similar in spirit to a basic ACL. Faster, but less intelligent.
- **Stateful firewall**: tracks the *state* of active connections (remembers the TCP three-way handshake from Lesson 7, tracks which sessions are legitimately established) — and can automatically allow return traffic for a connection that was initiated from the trusted side, without needing an explicit rule for it.

**Why it matters**: this is why, in most home/office networks, you can browse any website (outbound-initiated traffic) without configuring anything, but inbound connections from the internet are blocked by default — the stateful firewall only trusts traffic that's a legitimate response to something you initiated.

---

## Self-Check
1. What's the difference between a standard and extended ACL, and where should each be placed in a network?
2. Explain the "implicit deny" rule — what happens to traffic that doesn't match any ACL entry?
3. What are the three components of AAA, and what question does each one answer?
4. Why does DHCP snooping classify some ports as "trusted" and others as "untrusted"?
5. What's the practical difference between a stateless and a stateful firewall?

- [ ] ACLs — standard vs extended, placement rules, implicit deny
- [ ] AAA concept (Authentication, Authorization, Accounting)
- [ ] Port security (limiting MACs per port)
- [ ] DHCP snooping (basic concept)
- [ ] VPN basics (site-to-site vs remote access)
- [ ] Firewall basics (stateful vs stateless)
