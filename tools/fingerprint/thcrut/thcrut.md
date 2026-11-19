---
name: "thcrut"
categories:
  - blackarch-fingerprint
version: "1.2.5"
homepage: "https://github.com/vanhauser-thc/THC-Archive/"
packaged-by: [blackarch]
blackarch-builddate: "2021-07-28"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "vanhauser-thc/THC-Archive"
github-stars: 776
github-pushed: "2025-09-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# thcrut

> THC-RUT ("aRe yoU There"): van Hauser's high-speed network discovery and OS fingerprinting tool — ARP/ICMP/DHCP sweeps plus a `discover` mode that fingerprints operating systems across Class-B-sized ranges from TCP stack behaviour and service banners.

## What it's for

Situational awareness when landing on an unfamiliar network: find live hosts fast, then fingerprint their operating systems. The single `thcrut` binary dispatches to four modes — `discover` (host discovery plus OS fingerprinting), `arp`, `icmp` and `dhcp`. The fingerprinter combines TCP stack characteristics, banners grabbed from SNMP, telnet NVT negotiation, HTTP Server headers and DCE requests, port characteristics and timing values, and matches them through its own regular-expression signature matrix (also reading nmap-format OS fingerprints, which the BlackArch package ships under `/usr/share/thcrut/`). It keeps a state table with retransmits and is built for very large ranges — a Class B in minutes — with guidance to limit parallelism on fragile local segments. It requires root for raw sockets and does not do port scanning or exploitation; it answers "what is out there and what runs on it".

## Typical use cases

### Sweep a local segment with ARP

Find every live host on a LAN segment you are directly connected to.

```
thcrut arp 198.51.100.1-198.51.100.254
```

Outcome: sends ARP requests across the range and prints each responding IP with its MAC address and vendor lookup; keep parallelism modest (`-l 100`) on local networks so devices don't drop the request storm.

### Discover hosts and fingerprint their OS

Map a whole subnet and identify what each host runs.

```
thcrut discover -O 198.51.100.0/24
```

Outcome: runs high-speed host discovery, then fingerprints each live host's operating system; ranges accept `a.b.c.d-x.y.z.w`, CIDR notation, or `start-end` short form.

### Pin the interface and scan rate

Scan a specific NIC at a controlled pace on a touchy LAN.

```
thcrut -i eth0 -l 100 discover -O -v 198.51.100.1-198.51.100.254
```

Outcome: listens and transmits on `eth0`, probes at most 100 hosts in parallel, and in verbose mode prints fingerprint stamps alongside each identified OS; `-S` switches from the default spread mode to sequential scanning.

### Check liveness with ICMP only

Cheap ping-style confirmation without ARP or fingerprinting.

```
thcrut icmp 198.51.100.0/24
```

Outcome: sends ICMP probes across the range and lists the hosts that answer; the `dhcp` mode similarly sends spoofed DHCP/BOOTP requests to discover servers and configurations on the segment.

*No corpus evidence — derived from upstream documentation.*
