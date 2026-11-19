---
name: "netdiscover"
categories:
  - blackarch-recon
version: "257.42648f9"
homepage: "http://nixgeneration.com/~jaime/netdiscover/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-08-23"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "netdiscover-scanner/netdiscover"
github-stars: 403
github-pushed: "2025-08-14"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-09"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# netdiscover

> Active/passive ARP-based LAN address scanner: discovers live hosts on networks without a DHCP server to ask.

## What it's for

netdiscover (by Jaime Penalba) enumerates hosts on a local network using ARP. In active mode it injects ARP requests into given ranges and sniffs the replies; in passive mode it sends nothing and only listens to ARP traffic already on the wire — which also reveals hosts that never answer scans. It was written for wireless networks without a DHCP server (wardriving) but works on hubbed and switched networks alike; output is a live, scrollable table of IP, MAC, vendor (OUI), and host count. It needs root and a libpcap/libnet toolchain to build. If none of `-r`, `-l`, or `-p` is given it autoscan sweeps the common LAN ranges. It is a layer-2 host discovery tool — it does not port-scan or fingerprint services.

## Typical use cases

### Autoscan the common LAN ranges on an interface

Quick sweep when you do not know the addressing of the segment.

```
netdiscover -i eth0
```

Outcome: opens the interactive screen and starts ARP-requesting the common private ranges on `eth0`; each discovered host appears as a row (IP, MAC, vendor, count) that you can scroll with arrow keys or `j`/`k`, quitting with `q`.

### Scan a specific range

Map an exact subnet instead of the default common ranges.

```
netdiscover -i eth0 -r 198.51.100.0/24
```

Outcome: ARP-scans only that /24 and fills the same live table with responding hosts.

### Passive discovery only

Stay completely silent and learn who is talking.

```
netdiscover -i eth0 -p
```

Outcome: sends nothing; the table populates only from ARP traffic overheard on the segment — stealthy, and catches hosts that ignore scans, but needs existing traffic to learn anything.

### Machine-readable one-shot output

Produce a parseable host list for scripting instead of the curses screen.

```
netdiscover -i eth0 -r 198.51.100.0/24 -P -N
```

Outcome: after the active sweep, prints plain lines (IP, MAC, vendor, count) with no header (`-N`) and exits — `-P` stops after the scan, while `-L` keeps listening and appends hosts seen afterwards.

### Fast mode for a first pass

Probe only typical gateway/infrastructure addresses before a full sweep.

```
netdiscover -i eth0 -f
```

Outcome: scans only host addresses like `.1`, `.100`, `.254` per network, finishing far quicker; follow up with `-r` on the network it finds to enumerate the rest.

*No corpus evidence — derived from upstream documentation (usage/flags verified against the packaged source's `usage()` in src/main.c of netdiscover-scanner/netdiscover).*
