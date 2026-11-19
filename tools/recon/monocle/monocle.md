---
name: "monocle"
categories:
  - blackarch-recon
version: "1.0"
homepage: "http://packetstormsecurity.com/files/99823/Monocle-Host-Discovery-Tool.0.html"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2018-11-15"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# monocle

> ARP-based layer-2 host discovery for the local network, active or passive.

## What it's for

Monocle (by Vilmain Nicolas) answers "what hosts are alive on this segment" using ARP only: in active mode it sends ARP requests to the local /24 and collects the replies; in passive mode it sends nothing and just listens for ARP traffic already on the wire. The result is a table of IP and MAC address pairs — hosts that stay invisible to ping-based or higher-layer scans (firewalled, no IP stack of interest) still show up because ARP is required for Ethernet to function. It is a 2013-era C tool tested on Linux and FreeBSD; targets are expressed as last-octet numbers of the attached /24, not arbitrary CIDRs, and it is not a port scanner. No corpus evidence — derived from upstream documentation.

## Typical use cases

### Sweep the whole local subnet

Discover every live host on the segment.

```
sudo monocle
```

Outcome: active mode — ARP-requests every address of the attached /24 and prints a table of responding IP/MAC pairs (`-H` suppresses the header, `-s` adds packet statistics). Needs root for raw packet access.

### Scan selected hosts only

Target specific addresses within the /24.

```
sudo monocle 1,17,25,100-150
```

Outcome: probes only the listed last-octet values (single numbers, comma lists, and inclusive ranges like `100-150` combine freely) and reports the IP/MAC table for just those hosts.

### Listen passively for ARP traffic

Discover hosts without sending a single packet.

```
sudo monocle -p -w 30 -m 500
```

Outcome: passive mode (`-p`) — sniffs ARP requests and replies for 30 seconds (`-w`, wait time) or until 500 packets (`-m` max packets) are seen, then lists the observed IP/MAC pairs; stealthy, and also catches hosts talking among themselves.
