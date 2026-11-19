---
name: "simple-lan-scan"
categories:
  - blackarch-scanner
version: "1.0"
homepage: "http://packetstormsecurity.com/files/97353/Simple-LAN-Scanner-1.0.html"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# simple-lan-scan

> Original 2011 python2 ARP host-discovery script by Valentin Hoebel — pointer note.

Same lineage as [[simple-lan-scan3]]: this is the original "Simple LAN
Scanner" (python2 + scapy, released on PacketStorm in January 2011 by
Valentin Hoebel), which sweeps a CIDR with ARP and prints the MAC and IP
of every running system, plus a small log file. The python3 port by
jahway603 is the maintained form of the same single-option tool
(`--network=<cidr>` in both) and carries the full reference note;
this python2 package only remains for legacy environments. See
[[simple-lan-scan3]] for usage and output details.
