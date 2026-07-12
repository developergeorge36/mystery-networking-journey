# Lesson 8: Network Services

## Why This Lesson Matters

DHCP and DNS run silently in the background of nearly every network interaction you've ever had — you rarely notice them until they break, at which point troubleshooting them becomes a daily reality of network engineering. NAT/PAT is what makes the entire private-IP-addressing model (from Lesson 5) actually work on the real internet. NTP, Syslog, and SNMP round out the "quiet infrastructure" every managed network depends on.

---

## DHCP (Dynamic Host Configuration Protocol)

DHCP automatically assigns IP configuration to devices when they join a network — so nobody has to manually type in an IP address, subnet mask, default gateway, and DNS server on every single device.

### What DHCP Hands Out
- IP address
- Subnet mask
- Default gateway
- DNS server(s)
- Lease time (how long the device can keep this IP before renewing)
- Optionally: other settings like NTP server, domain name, etc.

### The DORA Process
DHCP works through a four-step exchange, remembered as **DORA**:

1. **Discover** — the client broadcasts: *"Is there a DHCP server out there? I need an IP address."* (Since the client has no IP yet, this must be a broadcast.)
2. **Offer** — a DHCP server responds with an offer: *"Here's an available IP address and configuration you can use."*
3. **Request** — the client broadcasts back, formally requesting that specific offered address (broadcast again, so any other DHCP servers on the network know their offer wasn't chosen).
4. **Acknowledge** — the server sends a final ACK, confirming the lease and configuration are now assigned.

### DHCP Scopes
A **scope** is the range of addresses a DHCP server is allowed to hand out for a given subnet (e.g., `192.168.1.100–192.168.1.200`), along with any excluded addresses (commonly reserved for static devices like servers/printers/routers).

### DHCP Relay
On networks with multiple subnets/VLANs, the DHCP server often isn't on the same local subnet as the client — but DHCP Discover messages are broadcasts, which don't cross routers by default. A **DHCP Relay Agent** (usually configured on the local router/Layer 3 switch) listens for these broadcasts and forwards them as unicast to the actual DHCP server, then relays the response back. This is a very common real-world design (one central DHCP server, many remote subnets).

**Why it matters practically**: seeing a device with a `169.254.x.x` (APIPA) address, as covered in Lesson 5, is the classic symptom of a failed DHCP process — the DORA exchange never completed successfully.

---

## DNS (Domain Name System)

DNS translates human-friendly domain names (`google.com`) into IP addresses that devices actually use to route traffic — think of it as the internet's phonebook.

### The Resolution Process (simplified)
1. Your device checks its local DNS cache first — has it already resolved this name recently?
2. If not, it queries its configured **DNS resolver** (often your ISP's, or a public one like `8.8.8.8`).
3. If the resolver doesn't already know the answer, it works up the DNS hierarchy: querying a **root server** → then a **TLD server** (e.g., for `.com`) → then the **authoritative server** for that specific domain, which finally returns the actual IP address.
4. The resolver caches and returns that IP address to your device, which then uses it to connect.

### Common DNS Record Types
| Record | Purpose |
|---|---|
| **A** | Maps a hostname to an IPv4 address |
| **AAAA** | Maps a hostname to an IPv6 address |
| **CNAME** | An alias — points one hostname to another hostname (not directly to an IP) |
| **MX** | Specifies the mail server responsible for a domain |
| **PTR** | Reverse lookup — maps an IP address back to a hostname |
| **NS** | Specifies the authoritative name servers for a domain |

**Why it matters practically**: "the website is down" is often actually a DNS problem, not a server problem — the `nslookup` or `dig` commands (you'll use these later) are the go-to tools for diagnosing whether a name is resolving correctly before you even start looking at the actual server or application.

---

## NAT vs PAT

Covered briefly in Lesson 5 (private IP ranges) — now the full picture.

### NAT (Network Address Translation)
Translates one private IP address to one public IP address, one-to-one. Useful when you have a small number of devices that each need their own dedicated public-facing identity, but it doesn't scale — you'd need as many public IPs as devices, which defeats the purpose of using private addressing in the first place.

### PAT (Port Address Translation) — aka "NAT Overload"
The far more common real-world implementation. **Many private IP addresses share a single public IP address**, and the router keeps them distinct by also translating the **port number** for each connection.

**How it works, conceptually**:
1. Device A (`192.168.1.10:51000`) wants to reach a website.
2. The router translates this to its own public IP with a unique port, e.g. `203.0.113.5:61001`, and keeps a translation table entry mapping the two.
3. Return traffic comes back addressed to `203.0.113.5:61001`; the router looks up its table and knows to forward it back to `192.168.1.10:51000`.
4. Every device behind the router shares that same public IP, distinguished only by port — which is exactly why this is called "PAT" instead of plain NAT.

This is literally what your home router does for every device in your house right now — dozens of devices, one public IP.

---

## NTP (Network Time Protocol) — Brief Awareness

NTP synchronizes clocks across all devices on a network to a common, accurate time source.

**Why it matters more than it sounds**: mismatched clocks break things in subtle, frustrating ways — certificate validation failures (HTTPS/TLS relies on accurate time), authentication protocols like Kerberos that are time-sensitive, and log correlation across devices (if you're troubleshooting an incident, you need every device's logs to agree on when things happened). Every properly managed network has NTP configured, syncing to either an internal time server or public time servers.

---

## Syslog & SNMP — Brief Awareness

These are the two pillars of network monitoring and logging — you'll go deeper on both later in your studies, but know what they are conceptually now.

- **Syslog**: a standard for devices to send log messages (errors, warnings, informational events) to a centralized logging server. Instead of manually checking logs on 50 individual switches, they all report to one place.
- **SNMP** (Simple Network Management Protocol): allows a central monitoring system to query devices for their status/performance data (CPU load, interface stats, temperature, etc.) and, in some cases, receive automatic alerts (**traps**) when something goes wrong.

**Why it matters practically**: these two protocols are the backbone of how real network operations centers (NOCs) actually know what's happening across hundreds or thousands of devices without manually checking each one.

---

## Self-Check
1. Walk through the DORA process, step by step, and explain why the first two steps must be broadcasts.
2. What problem does DHCP Relay solve, and why is it needed on networks with multiple subnets?
3. Explain the difference between NAT and PAT — why is PAT the far more common real-world implementation?
4. A user reports "the website won't load." What are the first two things you'd check, given what you now know about DNS and NAT?

- [ ] DHCP — DORA process, scopes, relay
- [ ] DNS — resolution process, record types (A, AAAA, CNAME, MX)
- [ ] NAT vs PAT
- [ ] NTP (time sync) — brief awareness
- [ ] Syslog & SNMP — brief awareness
