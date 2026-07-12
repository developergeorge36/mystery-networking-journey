# Lesson 13: Troubleshooting Methodology

## Why This Lesson Matters

Knowing theory and commands is one thing — knowing how to systematically find a problem under pressure, without guessing randomly, is what separates a technician from an engineer. This lesson pulls together everything from Lessons 1–12 into a repeatable process you can apply to almost any networking issue.

---

## Why Methodology Matters More Than Memorized Fixes

Random guessing ("let me try restarting the switch... let me try unplugging this cable...") wastes time and can even make problems harder to diagnose by changing multiple variables at once. A structured approach means:
- You isolate the actual cause instead of getting lucky
- You can explain *why* something broke, not just that it's fixed now
- You build a mental model that gets faster and sharper with every issue you solve

---

## The Three Core Approaches

### Bottom-Up (Layer 1 → Layer 7)
Start at the Physical layer and work upward, ruling out each layer before moving to the next.

1. **L1**: Is the cable plugged in? Is the link light green? Is the correct cable type being used?
2. **L2**: Does the switch see the device's MAC address (`show mac address-table`)? Is the port in the correct VLAN? Is it an access or trunk port as expected?
3. **L3**: Is the IP address/subnet mask correct? Is the default gateway correct and reachable? Does `show ip route` have the expected routes?
4. **L4**: Is the correct port being used? Is a firewall or ACL blocking that port?
5. **L7**: Is the application/service itself actually running and configured correctly?

**When to use it**: great for hands-on/physical issues, or when you have no strong hypothesis yet and want to be thorough and methodical — especially useful early in your career while you're still building intuition.

### Top-Down (Layer 7 → Layer 1)
The reverse — start at the application and work downward.

**When to use it**: useful when you suspect the issue is application-specific rather than a broad connectivity problem — e.g., "many users can reach everything else fine, but this one application is broken for everyone." Starting at L7 avoids wasting time re-verifying physical/L2/L3 connectivity that's probably already fine.

### Divide-and-Conquer
Jump into the *middle* of the stack (commonly Layer 3 — "can the device ping its default gateway?") and branch up or down depending on the result.

- If the ping to the gateway **fails** → the problem is at L3 or below → drop down and check IP config, then L2, then L1
- If the ping to the gateway **succeeds** → L1–L3 are fine → the problem is likely L4 or above → move up to check ports, ACLs, or the application itself

**When to use it**: this is usually the **fastest** real-world method once you have enough experience to make an educated first guess about where to start — most experienced engineers default to this.

---

## A Practical Troubleshooting Flow

1. **Define the problem clearly**: What exactly is failing? For whom? Since when? Is it everyone or just some users/devices? Vague problem statements ("the network is slow") lead to wasted effort — get specific before touching any configuration.

2. **Gather information**: Check logs, run `show` commands, ask the affected user precise questions ("when did this start," "did anything change recently," "does it happen on other devices too"). Recent changes are one of the most common root causes — always ask what changed.

3. **Form a hypothesis**: Based on the symptoms and the OSI layers, form a specific, testable theory about where the problem likely lives — don't jump straight to a fix without a theory of *why* it's happening.

4. **Test the hypothesis**: Use the appropriate approach (bottom-up, top-down, or divide-and-conquer) to confirm or rule out your theory. Change **one variable at a time** — changing multiple things simultaneously makes it impossible to know what actually fixed (or worsened) the issue.

5. **Implement a fix**: Once the actual cause is confirmed, make the change.

6. **Verify the fix**: Confirm the original symptom is actually resolved — don't assume a config change worked just because it looked right; test it the same way the original problem was reported.

7. **Document it**: What was the problem, what caused it, what fixed it. This is exactly the kind of entry that belongs in your `learning-log.md` — future you (or a future employer reading your GitHub) will benefit from seeing this kind of real diagnostic thinking, not just finished configs.

---

## Common Real-World Root Causes (Pattern Recognition)

As you gain experience, certain root causes come up constantly. Recognizing these patterns speeds up your hypothesis step significantly:

- **Duplex mismatch** (Lesson 3) → intermittent errors, degraded performance, not total failure
- **Wrong VLAN assignment** (Lesson 4) → device connects physically, gets an IP, but can't reach expected resources
- **Incorrect subnet mask or default gateway** (Lesson 5/6) → device can reach local subnet devices but nothing beyond it
- **ACL blocking unintended traffic** (Lesson 9) → very specific, selective failures (e.g., "I can ping it but can't RDP to it")
- **DHCP scope exhausted or DHCP relay misconfigured** (Lesson 8) → new devices get APIPA addresses (`169.254.x.x`) while existing devices are fine
- **STP blocking a port unexpectedly** (Lesson 4) → a link that should be active shows as blocking/down for no obvious physical reason
- **Native VLAN mismatch on a trunk** (Lesson 4) → intermittent, hard-to-explain connectivity issues across specific VLANs only

---

## Self-Check
1. Explain the difference between bottom-up, top-down, and divide-and-conquer troubleshooting, and give a scenario where each would be the best choice.
2. Why is it important to change only one variable at a time while troubleshooting?
3. A new employee's laptop gets an IP address in the `169.254.x.x` range. Using pattern recognition, what's your first hypothesis, and why?
4. Why does documenting a fixed issue matter, beyond just solving the immediate problem?

- [ ] Bottom-up vs top-down vs divide-and-conquer approach
- [ ] OSI-layer-based troubleshooting logic
