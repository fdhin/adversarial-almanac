---
name: "gwcheck"
categories:
  - blackarch-networking
version: "0.1"
homepage: "http://packetstormsecurity.com/files/62047/gwcheck.c.html"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-15"
build-system: other
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Seabreg/gwcheck"
github-stars: 0
github-pushed: "2019-09-09"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gwcheck

> Finds which LAN hosts actually route to the Internet by testing each candidate's MAC address with a TCP SYN aimed at an external host.

## What it's for

gwcheck (poplix, v0.1, 2007) is a small libpcap-based C program that decides whether a host on the local Ethernet is a gateway to the Internet. It resolves the candidate's MAC address, then sends a TCP SYN to an external Internet host addressed to that MAC: if a reply comes back, the host forwarded the packet and is a gateway. It accepts a single IPv4 address or a file of addresses (one per line, conveniently generated from an nmap ARP ping sweep), so it doubles as a gateway scanner for a whole subnet. IPv4 and Ethernet only, Linux and BSD.

## Typical use cases

### Test a single host

```
sudo gwcheck 198.51.100.10
```

Outcome: prints the ARP exchange and the TCP probe result for the host, ending in a verdict on whether it is an Internet gateway; add `-q` to show gateways only.

### Sweep a subnet's live hosts for gateways

```
sudo gwcheck -q hosts.txt
```

Outcome: reads one IPv4 address per line and prints only the hosts that pass the gateway test. The input file is one command away from a sweep: `nmap -sP -PR -oG nmap_file 198.51.100.* && grep Host nmap_file | awk '{print $2}' > hosts.txt` lists all ARP-responding hosts. `-i` picks the interface, `-I` and `-p` the external host and port probed, `-t` and `-T` tune the ARP and TCP timeouts.

*No corpus evidence — derived from upstream documentation.*

*GitHub mirror: `Seabreg/gwcheck` — a faithful mirror, byte-identical to the PacketStorm-packaged gwcheck.c by poplix.*
