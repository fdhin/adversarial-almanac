---
name: "sps"
categories:
  - blackarch-networking
version: "4.3"
homepage: "https://sites.google.com/site/simplepacketsender/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-09-12"
build-system: make
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "pdbuchan/sps"
github-stars: 1
github-pushed: "2026-09-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 5.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 22
gh-activity-checked: "2026-11-09"
---

# sps

> GTK workbench for hand-crafting and sending IPv4/IPv6 packets, headers and all.

## What it's for

Simple Packet Sender (SPS), written by Hohlraum, is a GUI packet-crafting tool: build a packet field by field — Ethernet, IPv4 or IPv6 with extension headers (hop-by-hop, routing, fragmentation, AH, ESP), TCP/UDP/ICMP details, spoofed source address and port — then send it and iterate. It computes checksums and sequence numbers automatically, saves and loads packet definitions to files, exports to Wireshark, and includes IPv4/IPv6 traceroute plus ASN/IP-delegation lookup helpers. There is no command-line interface: everything happens in the GTK window, making it a visual alternative to script-driven crafters for protocol experiments and firewall testing.

## Typical use cases

### Launch the crafting workbench

```
sps
```

Outcome: opens the SPS GTK window; select a network interface, fill in the IPv4 or IPv6 header and payload fields (checksums are computed for you), and use the send controls to transmit the crafted packet while the packet view shows exactly what went on the wire.

*No corpus evidence — derived from upstream documentation.*
