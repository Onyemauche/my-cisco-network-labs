# Lab 01 — Troubleshooting Guide

Use evidence to isolate the fault. Avoid changing multiple settings at the same time.

## Recommended troubleshooting order

1. **Physical/link layer:** confirm the correct ports are connected, devices are powered on, and links are active.
2. **Router interface state:** run `show ip interface brief`; confirm G0/0 and G0/1 have the intended addresses and show `up/up`.
3. **Host addressing:** check each PC's IP address, subnet mask, and default gateway in Desktop → IP Configuration.
4. **Local gateway reachability:** ping each PC's own gateway.
5. **Routing:** run `show ip route` and confirm both `/24` networks are connected.
6. **End-to-end reachability:** ping the remote gateway, then the remote PC.
7. **Change one item, retest, and record the result.**

## Fault A — R1's LAN B interface is administratively down

**Introduce the fault on R1:**

```text
enable
configure terminal
interface GigabitEthernet0/1
shutdown
end
```

**Symptoms:** G0/1 should show `administratively down`; PC-B cannot reach R1's LAN B interface or PC-A. The LAN A gateway may still be reachable from PC-A.

**Diagnose:**

```text
show ip interface brief
show ip route
```

**Fix:**

```text
enable
configure terminal
interface GigabitEthernet0/1
no shutdown
end
```

Wait for the interface to return to `up/up`, then rerun the pings. Record the actual output in your lab notes.

## Fault B — PC-B has the wrong default gateway

**Introduce the fault:** change PC-B's default gateway from `192.168.20.1` to `192.168.20.254`.

**Expected symptoms:** PC-B should still be able to ping `192.168.20.1` because that address is on the same subnet. However, traffic to PC-A will fail because PC-B tries to send off-subnet traffic to the incorrect next hop.

**Diagnose:** compare PC-B's IP Configuration with the addressing plan. The default gateway must be `192.168.20.1` and must be on the same subnet.

**Fix:** restore the gateway to `192.168.20.1`, then ping `192.168.10.10` again.

## Fault C — PC-B uses an incorrect subnet mask

**Introduce the fault:** set PC-B's mask to `255.255.0.0` (`/16`) instead of `255.255.255.0` (`/24`).

**Expected symptoms:** PC-B treats `192.168.10.10` as directly connected and attempts to resolve it on the local LAN rather than sending the packet to its gateway. The ping to PC-A fails, while the local gateway may still answer.

**Diagnose:** compare the mask against the addressing plan. A host's subnet mask changes which destinations it regards as local.

**Fix:** restore `255.255.255.0` on PC-B and rerun the ping to PC-A.

## Troubleshooting worksheet

For every fault, record:

- **Symptom:** what failed?
- **Evidence:** which command or test demonstrated it?
- **Root cause:** what setting or condition caused it?
- **Correction:** what exact change fixed it?
- **Retest:** which test passed afterward?
- **Learning:** what would you check first next time?

Do not publish fabricated output. A screenshot of an actual failed test and the corrected result is better evidence than an unsupported claim that the lab works.
