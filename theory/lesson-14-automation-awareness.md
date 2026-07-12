# Lesson 14: Automation Awareness

## Why This Lesson Matters

Modern CCNA (200-301) includes network programmability and automation because the industry has genuinely shifted this direction — large networks are increasingly managed through code and APIs rather than manually typing commands into every single device one at a time. You don't need to become a software developer, but you're expected to understand the concepts and why they matter. This is intentionally the lightest-weight lesson in the curriculum — conceptual awareness, not deep implementation.

---

## Why Automation Matters in Networking

Imagine a company with 500 switches across dozens of locations, and a new security policy requires a config change on every single one. Manually logging into each device (Lesson 12's CLI, one device at a time) doesn't scale — it's slow, error-prone, and inconsistent (easy to make a typo on device #347 that doesn't happen on the other 499).

Automation solves this by letting engineers define a change **once**, in code, and apply it consistently and repeatably across many devices at once.

---

## JSON Basics

**JSON (JavaScript Object Notation)** is a lightweight, structured data format — the near-universal way that APIs (including network device APIs) send and receive data today.

Structure: **key-value pairs**, nested in objects (`{}`) and arrays (`[]`).

```json
{
  "hostname": "SW1",
  "interfaces": [
    {
      "name": "GigabitEthernet0/1",
      "status": "up",
      "vlan": 10
    },
    {
      "name": "GigabitEthernet0/2",
      "status": "down",
      "vlan": 20
    }
  ]
}
```

- Each **key** (like `"hostname"`) maps to a **value** (like `"SW1"`)
- Values can be text (strings), numbers, true/false (booleans), or nested structures (objects/arrays) — like the `interfaces` array above, which holds multiple objects, each describing one interface

**Why it matters practically**: when a network device exposes an API (instead of just a CLI), querying it for information — or pushing configuration to it — typically involves sending and receiving data in this exact format. Being able to read a JSON structure and understand what it represents is the baseline skill here.

---

## REST API Concept

A **REST API** is a standardized way for one system to communicate with another over HTTP — the same underlying protocol your browser uses to load web pages, but used here for machines talking to machines.

The core actions (HTTP methods) you'll see referenced:
- **GET** — retrieve information (e.g., "give me the current status of this interface")
- **POST** — create something new (e.g., "add this new VLAN")
- **PUT/PATCH** — update existing information (e.g., "change this interface's description")
- **DELETE** — remove something (e.g., "delete this ACL entry")

**Practical example (conceptual)**: instead of logging into a switch's CLI and manually running `show ip interface brief`, a script could send a **GET** request to that switch's API endpoint and receive the same information back as structured JSON — which can then be automatically parsed, checked, logged, or acted upon programmatically, across hundreds of devices, without a human manually checking each one.

**Why it matters**: this is the foundation of how modern network monitoring dashboards, automation scripts, and orchestration platforms actually interact with devices behind the scenes — even if you're just clicking a button in a dashboard, that button is very likely triggering REST API calls underneath.

---

## What Ansible/Python Automation Is Used For (Conceptual Only)

You don't need to write this code yet — just understand what these tools are for and why network engineers increasingly need to be aware of them.

### Python
A general-purpose programming language, widely used in networking to:
- Write scripts that connect to multiple devices and pull information (e.g., checking interface status across 200 switches and generating a report)
- Automate repetitive configuration tasks
- Interact with device APIs (using JSON/REST, as above) to query or push changes programmatically

### Ansible
A configuration management and automation tool, commonly used in networking to:
- Define the *desired state* of a device's configuration in a simple, human-readable file (YAML format)
- Push that configuration out to many devices at once, consistently, without manually touching each one's CLI
- Widely used specifically because it doesn't require an agent installed on the target device — it connects via SSH and pushes configuration, which fits naturally with how network devices are traditionally managed

**Why it matters**: this is the direction the industry keeps moving — from "engineer manually configures each device" toward "engineer defines the desired configuration once, and automation applies/enforces it across the fleet." Even if your first job is heavily manual/CLI-based (very common early in a career), being aware of this trend — and having even beginner-level exposure to it — is a meaningful differentiator for someone building a public portfolio like yours.

---

## Self-Check
1. What problem does automation solve when managing hundreds of network devices, compared to manual CLI configuration?
2. In a JSON object, what's the difference between a key and a value? Give a simple example.
3. What's the difference between a GET request and a POST request in a REST API context?
4. Conceptually, what does Ansible do differently from writing a custom Python script to configure devices?

- [ ] JSON basics (structure, key-value pairs)
- [ ] REST API concept (GET/POST, basic idea)
- [ ] What Ansible/Python automation is used for in networking (conceptual only)
