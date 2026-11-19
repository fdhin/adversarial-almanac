---
name: "nacker"
categories:
  - blackarch-networking
version: "23.b67bb39"
homepage: "https://github.com/carmaa/nacker"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "carmaa/nacker"
github-stars: 105
github-pushed: "2014-09-24"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nacker

> Bypasses wired 802.1x Network Access Control by finding hosts the switch exempts from authentication and impersonating them.

## What it's for

Nacker (Carsten Maartmann-Moe, 2013) automates one classic NAC bypass on a switched wired LAN. It inventories the local subnet with an ARP sweep, sorts the live hosts so that likely-exempt devices (printers, conferencing gear, monitoring boxes that cannot speak 802.1x) come first, then spoofs a chosen host's MAC address and requests DHCP as that machine — riding the port exception IT granted to the exempt device. It is a Python 2 script built on Scapy (plus netaddr/netifaces), must run as root on the segment, and takes no arguments beyond help. The upstream README states the tool "is currently in a non-working state", so treat it as a reference implementation of the MAC-impersonation technique rather than a turn-key bypass; it does not address wireless 802.1x or any enforcement beyond MAC-based exemptions.

## Typical use cases

### Attempt a MAC-impersonation NAC bypass on the local segment

```
sudo nacker
```

Outcome: prints a banner, picks the first IP-configured interface, ARP-sweeps the subnet and reports live hosts, orders them printers-first/laptops-last by OUI, baselines connectivity with a top-10-port SYN scan, then walks the list spoofing each MAC and running DHCP. If the baseline scan finds open ports it exits with "Are you sure you are on a LAN that is protected by NAC?" — no bypass needed.

### Show the usage screen

```
nacker -h
```

Outcome: prints the tool's usage text — the only flag the script accepts — and exits without touching the network.

*No corpus evidence — derived from upstream documentation.*
