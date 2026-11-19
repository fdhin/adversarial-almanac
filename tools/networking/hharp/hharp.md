---
name: "hharp"
categories:
  - blackarch-networking
version: "1beta"
homepage: "http://packetstormsecurity.com/files/81368/Hackers-Hideaway-ARP-Attack-Tool.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/hharp"
github-stars: 0
github-pushed: "2019-09-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hharp

> Scapy-based ARP attack tool with passive MITM, flood MITM, and switch CAM-flooding modes.

## What it's for

hharp ("Hacker's Hideaway ARP attack tool", Anarchy Angel, 1 beta, 2009) is a Python 2 + scapy script for man-in-the-middle and switch flooding on the local network. Mode 1 passively answers every ARP request on the wire to redirect all clients' traffic through this host; mode 2 does the same for one target; mode 3 floods a target with forged ARP replies; mode 4 floods a switch's CAM table with random source MACs until it forwards like a hub. The script enables IPv4 forwarding before attacking and restores it on Ctrl+C, and `-r` can point the redirected traffic at a machine other than the attacker. Needs root, and only affects IPv4 over Ethernet.

## Typical use cases

### Passively MITM every client on the network

```
sudo hharp -m 1
```

Outcome: enables IP forwarding, then watches ARP who-has requests and answers each requester claiming this host (or the `-r` MAC) owns the requested address; client traffic starts flowing through this machine — run a sniffer in parallel, and Ctrl+C stops the attack and undoes the forwarding change.

### Passively MITM a single target

```
sudo hharp -m 2 -t 198.51.100.10
```

Outcome: the same redirection as mode 1 but restricted to the host at 198.51.100.10 — quieter and less disruptive on shared segments.

### Flood a target's ARP cache

```
sudo hharp -m 3 -t 198.51.100.10 -d 198.51.100.1
```

Outcome: continuously blasts forged ARP replies at the target in batches of ten, claiming this host (or the `-r` MAC) is the route to 198.51.100.1; `-s <MAC>` supplies the target's MAC directly when ARP resolution fails.

### Turn a switch into a hub

```
sudo hharp -m 4 -t 198.51.100.1
```

Outcome: floods the switch at 198.51.100.1 with ARP frames carrying random IP/MAC pairs until its CAM table overflows and it broadcasts all traffic, making every frame on the segment sniffable; Ctrl+C stops the flood.

*No corpus evidence — derived from upstream documentation.*
