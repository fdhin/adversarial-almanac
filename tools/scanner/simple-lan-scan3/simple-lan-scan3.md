---
name: "simple-lan-scan3"
categories:
  - blackarch-scanner
version: "1.0.1"
homepage: "https://codeberg.org/jahway603/Simple-LAN-Scanner3"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# simple-lan-scan3

> Minimal scapy-based ARP sweep that lists the MAC and IP of every live host on a LAN.

## What it's for

Simple-LAN-Scanner3 (by jahway603) is a python3 port of Valentin Hoebel's
2011 python2 "Simple LAN Scanner" — same lineage, same single option. It
takes one CIDR, ARPs every address through scapy, and prints the MAC and
IP addresses of the systems that answer, also writing a small log file.
It is deliberately the smallest useful host-discovery step: no port
scanning, no OS detection, no service probes — run it first to get the
live-host list, then hand that list to a port scanner. It needs root for
raw ARP packets. The python2 original is kept as a separate package, see
[[simple-lan-scan]].

## Typical use cases

### Discover live hosts on a subnet

One option, one pass.

```
sudo simple-lan-scan3 --network=198.51.100.0/24
```

Outcome: every responding host in the /24 is printed with its IP and MAC
address as the ARP replies arrive, and a small log file of the results is
written to the working directory; the printed list is the input your port
scanner wants. `--help` prints the same usage with the feature summary.

*No corpus evidence — derived from upstream documentation (Codeberg README and packaged source).*
