# Lesson 5: IP Addressing & Subnetting

## Why This Lesson Matters Most

This is the single most heavily weighted skill on the CCNA exam and the most-used skill in real network engineering work. Everything else — routing, VLANs, ACLs, NAT — depends on being fluent in IP addressing and subnetting. Don't rush this lesson; budget real practice time (daily, on paper, no calculator) before moving on.

---

## IPv4 Structure

An IPv4 address is **32 bits**, written as four 8-bit sections ("octets") separated by dots, each octet 0–255 in decimal.

```
192.168.1.10
= 11000000.10101000.00000001.00001010  (binary)
```

Every IP address has two logical parts:
- **Network portion** — identifies which network the device belongs to
- **Host portion** — identifies the specific device within that network

The **subnet mask** defines where the split between network and host bits happens.

### Binary-to-Decimal Conversion (know this cold)

Each octet is 8 bits, with place values:
```
128  64  32  16  8  4  2  1
```
To convert binary to decimal, add up the place values where there's a `1`.

Example: `11000000` → 128 + 64 = **192**

Practice converting random 8-bit binary numbers to decimal (and back) until it's fast and automatic — this is the building block for every subnetting calculation you'll ever do.

---

## Public vs Private IP Ranges

**Private ranges** (not routable on the public internet — used inside LANs):
| Range | CIDR | Common Use |
|---|---|---|
| 10.0.0.0 – 10.255.255.255 | /8 | Large enterprise networks |
| 172.16.0.0 – 172.31.255.255 | /12 | Mid-size networks |
| 192.168.0.0 – 192.168.255.255 | /16 | Home networks, small labs |

**Public IPs**: everything else (with a few reserved exceptions) — globally unique, routable across the internet, usually assigned by an ISP. This is why **NAT** exists — to translate private addresses to a public one when traffic leaves your LAN.

### Other special ranges worth knowing
- `127.0.0.0/8` — loopback (127.0.0.1 = localhost, always refers to your own device)
- `169.254.0.0/16` — **APIPA** (Automatic Private IP Addressing) — a device gives itself one of these when it fails to get an address from DHCP. Seeing a 169.254.x.x address is a classic sign of a DHCP problem.
- `0.0.0.0` — represents "any address" or is used as the default route
- `255.255.255.255` — limited broadcast address

---

## Subnet Masks & CIDR Notation

A subnet mask marks which bits are network bits (1s) and which are host bits (0s).

**CIDR notation** (`/24`, `/26`, etc.) is shorthand for "how many bits are set to 1 in the mask," and it's the notation you'll use constantly.

| CIDR | Subnet Mask | Binary (last relevant octet) |
|---|---|---|
| /24 | 255.255.255.0 | 00000000 |
| /25 | 255.255.255.128 | 10000000 |
| /26 | 255.255.255.192 | 11000000 |
| /27 | 255.255.255.224 | 11100000 |
| /28 | 255.255.255.240 | 11110000 |
| /29 | 255.255.255.248 | 11111000 |
| /30 | 255.255.255.252 | 11111100 |

---

## Subnetting Calculations (The Core Skill)

**Two formulas to memorize:**

- **Number of subnets** = 2^(borrowed bits)
- **Number of usable hosts per subnet** = 2^(remaining host bits) − 2

The "−2" accounts for the **network address** (all host bits = 0, identifies the subnet itself, unusable by a device) and the **broadcast address** (all host bits = 1, used to reach every device in that subnet).

### Worked Example
You're given `192.168.1.0/24` and need to create subnets that each support at least 50 hosts.

1. /24 gives 8 host bits (256 total addresses, 254 usable). Too big — we want smaller subnets.
2. We need at least 50 usable hosts → find the smallest host-bit count where 2^n − 2 ≥ 50.
   - 2^6 − 2 = 62 ✓ (6 host bits works)
3. If 6 bits are host bits, that means 2 bits were borrowed from the original 8, giving us a **/26** (24 + 2 = 26).
4. Subnet mask for /26 = `255.255.255.192`
5. Block size = 256 − 192 = **64** → subnets increment by 64:
   - `192.168.1.0/26` → usable: .1–.62, broadcast: .63
   - `192.168.1.64/26` → usable: .65–.126, broadcast: .127
   - `192.168.1.128/26` → usable: .129–.190, broadcast: .191
   - `192.168.1.192/26` → usable: .193–.254, broadcast: .255

**The "magic number" trick**: 256 − (last non-zero octet of the mask) = your block size / increment. This is the fastest real-world method — practice it until it's second nature.

---

## VLSM (Variable Length Subnet Masking)

Real networks rarely use one uniform subnet size everywhere — that wastes addresses. **VLSM** means using different subnet sizes tailored to how many hosts each segment actually needs.

Example: a company network might need:
- 100 hosts for the main office LAN → needs a /25 (126 usable)
- 20 hosts for a smaller branch → a /27 (30 usable) is enough
- 2 hosts for a router-to-router WAN link → a /30 (2 usable) is perfect, no waste

**Rule of thumb for router links specifically**: always use a **/30** (or `/31` in more modern point-to-point designs) — using a /24 for a link that only ever has 2 devices wastes 252 addresses for nothing.

**Why it matters**: This is exactly how real ISPs and enterprises design address space — efficiently, sized to actual need, not one-size-fits-all. VLSM questions are common on the CCNA exam.

---

## Route Summarization (Basics)

When multiple subnets can be represented by a single, larger summary route, routers can advertise just the summary instead of every individual subnet — reducing the size of routing tables.

Example: `192.168.0.0/24`, `192.168.1.0/24`, `192.168.2.0/24`, `192.168.3.0/24` can all be summarized as a single route: `192.168.0.0/22` (since all four subnets fall within that /22 block).

At this stage, just understand the concept — you'll apply it more directly once you're deeper into routing protocols.

---

## IPv6 Basics

IPv4's ~4.3 billion addresses are running out globally — **IPv6** solves this with a vastly larger address space.

- **128 bits** total (vs IPv4's 32), written in 8 groups of 4 hex digits, separated by colons:
  ```
  2001:0db8:0000:0000:0000:ff00:0042:8329
  ```
- **Shorthand rules**:
  - Leading zeros in each group can be dropped: `0db8` → `db8`
  - One consecutive run of all-zero groups can be collapsed to `::` (only once per address)
  - Example: `2001:0db8:0000:0000:0000:ff00:0042:8329` → `2001:db8::ff00:42:8329`

### Address Types
- **Unicast**: one-to-one, delivered to a single specific interface (like IPv4's normal addressing)
- **Multicast**: one-to-many, delivered to a defined group of interfaces
- **Anycast**: one-to-nearest, delivered to the closest interface in a group (based on routing distance) — no equivalent in traditional IPv4
- Note: IPv6 has **no broadcast** — multicast replaces that function entirely

**Why it matters now**: CCNA expects working IPv6 knowledge, and IPv6 adoption keeps growing globally. You don't need to master it as deeply as IPv4 subnetting yet, but know the format and address types cold.

---

## Self-Check
1. Convert `11010110` to decimal, and convert `202` to binary — without a calculator.
2. Given `192.168.10.0/24`, subnet it to support 4 equal-sized subnets. What's the new mask, and what are the 4 network addresses?
3. Why is a /30 the standard choice for a router-to-router link instead of a /24?
4. What does `::` represent in an IPv6 address, and how many times can it appear in a single address?

- [ ] IPv4 structure, binary-to-decimal conversion
- [ ] Public vs private IP ranges
- [ ] Subnet masks & CIDR notation
- [ ] Subnetting calculations (manual, no calculator)
- [ ] VLSM (Variable Length Subnet Masking)
- [ ] Route summarization basics
- [ ] IPv6 basics: address format, types, shorthand notation
