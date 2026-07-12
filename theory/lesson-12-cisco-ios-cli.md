# Lesson 12: Cisco IOS / CLI Foundations

## Why This Lesson Matters

This is where theory turns into hands-on ability. Everything you've learned so far — VLANs, routing, ACLs — eventually gets typed into a device's command-line interface. Fluency with the CLI structure and core commands is what lets you move fast and confidently in Packet Tracer (and later, real hardware) instead of hunting for syntax every time.

---

## CLI Modes (The Hierarchy You'll Live In)

Cisco IOS is structured in nested modes, each granting access to different commands. You move deeper into configuration as you go, and each mode is visually indicated by the prompt.

```
Router>                    User EXEC mode
Router#                    Privileged EXEC mode
Router(config)#            Global Configuration mode
Router(config-if)#         Interface Configuration mode
```

### User EXEC Mode (`>`)
- The mode you land in immediately after connecting
- Very limited — basic monitoring commands only (e.g., `ping`, `show` a small subset of info)
- No ability to view full configuration or make changes

**Enter privileged mode**: `enable`

### Privileged EXEC Mode (`#`)
- Full visibility into the device — all `show` commands, full diagnostics
- Still **read-only** in terms of configuration — you can look at everything, but not change it yet
- This is also where you save configuration changes

**Enter global config mode**: `configure terminal` (often abbreviated `conf t`)

### Global Configuration Mode (`(config)#`)
- Where device-wide settings are configured: hostname, passwords, VLANs, static routes, ACLs (the ACL body itself), etc.
- Changes here take effect **immediately** on the running configuration (unlike some other vendors that require an explicit "commit")

**Enter interface config mode**: `interface <type><number>` (e.g., `interface gigabitEthernet0/0`, often abbreviated `int gi0/0`)

### Interface Configuration Mode `(config-if)#`
- Where you configure settings specific to one interface: IP address, VLAN assignment (access/trunk), enabling/disabling the interface, description, etc.

**Moving back up a level**: `exit` (steps back one level). `end` or `Ctrl+Z` jumps straight back to Privileged EXEC from anywhere.

---

## Core Commands to Build Muscle Memory On

### Viewing Configuration & Status
| Command | What it shows |
|---|---|
| `show running-config` | The current active configuration (in memory) |
| `show startup-config` | The configuration saved to be loaded on next boot |
| `show ip interface brief` | Quick summary of all interfaces — IP address, status (up/down), protocol status |
| `show vlan brief` | VLANs configured on a switch and which ports belong to each |
| `show mac address-table` | The switch's learned MAC-to-port mappings (Lesson 3) |
| `show ip route` | The router's routing table (Lesson 6) |
| `show interfaces` | Detailed per-interface stats: errors, duplex, speed, packet counts |

### Making Changes
| Command | Purpose |
|---|---|
| `hostname <name>` | Sets the device's name (global config mode) |
| `ip address <ip> <mask>` | Assigns an IP address to an interface (interface config mode) |
| `no shutdown` | Enables an interface (interfaces are administratively down by default until this is run) |
| `shutdown` | Administratively disables an interface |
| `interface vlan <id>` | Creates/enters an SVI for inter-VLAN routing on a Layer 3 switch |
| `switchport mode access` / `switchport mode trunk` | Sets a port as access or trunk (Lesson 4) |

### Saving Your Work
```
copy running-config startup-config
```
(often abbreviated `copy run start`)

**This is critical and easy to forget**: changes made in the CLI take effect immediately in the running configuration, but are **lost on reboot** unless explicitly saved to the startup configuration with this command. This is one of the most common early mistakes — spending an hour configuring a device, then losing everything because it wasn't saved before a reload.

---

## Helpful CLI Behaviors Worth Knowing

- **Tab completion**: typing a partial command and pressing `Tab` auto-completes it if unambiguous (e.g., `conf` + Tab → `configure`)
- **`?`**: at any point, typing `?` shows available commands/options from your current position — genuinely one of the most useful habits to build early, since it turns the CLI into its own reference guide
- **Abbreviations**: most commands can be abbreviated to the shortest unambiguous form (e.g., `sh ip int br` instead of `show ip interface brief`) — experienced engineers use these constantly
- **`no` prefix**: adding `no` in front of most commands reverses/removes that configuration (e.g., `no shutdown` enables an interface; `no ip address 192.168.1.1 255.255.255.0` removes that specific address)

---

## Self-Check
1. List the four CLI modes in order, from least to most privileged/specific, and the command used to move from each one to the next.
2. What's the difference between `show running-config` and `show startup-config`, and why does that distinction matter after a reboot?
3. Why is an interface down by default even after you've assigned it an IP address, and what command fixes that?
4. What happens if you make configuration changes but never run `copy running-config startup-config` before the device reboots?

- [ ] CLI modes: User EXEC → Privileged EXEC → Global Config → Interface Config
- [ ] Core commands: `show running-config`, `show ip interface brief`, `show vlan brief`, `show mac address-table`, `show ip route`
- [ ] Saving config: `copy running-config startup-config`
