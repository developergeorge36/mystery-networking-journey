# Lesson 10: Wireless

## Why This Lesson Matters

Wireless networking is everywhere in modern environments — offices, campuses, homes — and CCNA expects you to understand how it works conceptually, even though deep wireless design (RF planning, controller configuration) is more of a specialization. This lesson covers the fundamentals: what makes wireless different from wired, security standards, and the enterprise AP model.

---

## SSID

The **SSID (Service Set Identifier)** is the human-readable name of a wireless network — what you see when you search for Wi-Fi on your phone or laptop (e.g., "HomeNetwork" or "Office-Guest").

- A single access point can broadcast **multiple SSIDs** simultaneously (e.g., a corporate SSID and a separate guest SSID), each typically mapped to a different VLAN — this ties directly back to Lesson 4's VLAN concepts. The guest network is isolated from internal resources purely through VLAN separation, even though both SSIDs come from the same physical AP.
- SSIDs can be **broadcast** (visible in scan lists) or **hidden** (not broadcast — the client must already know the exact name to connect). Hiding an SSID is a very weak security measure on its own — it doesn't encrypt anything, it just avoids showing up in a casual scan.

---

## Wireless Security Standards

Security here means how the wireless connection is authenticated and encrypted.

| Standard | Status | Notes |
|---|---|---|
| **WEP** | Broken — never use | Original wireless security standard, cryptographically broken; can be cracked in minutes with widely available tools |
| **WPA** | Legacy | Improvement over WEP, but has known weaknesses; largely phased out |
| **WPA2** | Still very common | Uses AES encryption, the standard for most networks in use today |
| **WPA3** | Current standard | Stronger encryption, better protection against offline password-guessing attacks, should be preferred for any new deployment |

**Why it matters practically**: if you're ever asked to secure or audit a wireless network, checking which standard is in use is one of the very first things to verify — a network still running WEP (or worse, open/no security) is a serious, immediate vulnerability.

---

## Frequency Bands

Wireless communicates over specific radio frequency ranges, each with tradeoffs:

- **2.4 GHz**
  - Longer range, better at penetrating walls/obstacles
  - More prone to interference — shared with many other devices (Bluetooth, microwaves, cordless phones, neighboring networks)
  - Fewer non-overlapping channels available, so congestion is common in dense areas
  - Slower maximum speeds compared to 5GHz/6GHz

- **5 GHz**
  - Faster speeds, more available channels, less interference/congestion
  - Shorter range and worse at penetrating obstacles than 2.4GHz
  - Now the default preference for most modern devices when signal strength allows

- **6 GHz** (Wi-Fi 6E / Wi-Fi 7)
  - Newest band, even more channel space and lower interference
  - Shortest range of the three, requires newer hardware to support it

**Why it matters practically**: this tradeoff (range vs. speed vs. interference) directly informs real AP placement decisions — a large open warehouse might lean more on 2.4GHz for coverage, while a dense office with many APs close together would lean on 5GHz/6GHz to reduce interference and maximize speed.

---

## Autonomous AP vs Controller-Based AP (WLC)

This is the key architectural concept CCNA focuses on for wireless.

### Autonomous AP
- A standalone access point — each one is individually configured and managed on its own
- Fine for home use or very small deployments (a handful of APs)
- Doesn't scale well: imagine manually configuring and updating 200 individual APs across a large office building — every SSID/security change means touching every single device

### Controller-Based AP (Lightweight AP + WLC)
- In enterprise environments, a central **Wireless LAN Controller (WLC)** manages many "lightweight" APs at once
- APs register with the WLC and receive their configuration centrally — SSIDs, security settings, channel/power assignments — all pushed from one place
- The WLC also handles more advanced coordination: managing channel overlap between nearby APs, load balancing clients, and enabling seamless roaming (a client walking across a building stays connected as it moves from one AP's coverage to the next, without dropping)
- **CAPWAP** (Control And Provisioning of Wireless Access Points) is the protocol used between lightweight APs and the WLC to carry this management traffic and tunnel client data back to the controller

**Why it matters**: this is the model virtually all real enterprise wireless deployments use today, and it's the wireless architecture CCNA expects you to understand conceptually — even if deep WLC configuration is a more specialized skill you'd build later.

---

## Self-Check
1. Can a single access point broadcast multiple SSIDs, and if so, how are they typically kept isolated from each other?
2. Why is WEP considered unusable today, and what replaced it?
3. What's the tradeoff between 2.4GHz and 5GHz, and how would that inform AP placement in a large warehouse vs. a dense office?
4. What problem does a Wireless LAN Controller (WLC) solve that becomes unmanageable with many autonomous APs?

- [ ] SSID, WPA2/WPA3
- [ ] 2.4GHz vs 5GHz vs 6GHz tradeoffs
- [ ] Autonomous AP vs WLC (controller-based) model
