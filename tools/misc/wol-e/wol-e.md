---
name: "wol-e"
categories:
  - blackarch-misc
version: "2.0"
homepage: "https://code.google.com/archive/p/wol-e/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "RutAnimalJunk/wol-e"
github-stars: 5
github-pushed: "2016-03-22"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wol-e

> Wake-on-LAN attack suite (Python 2): power on or brute-force WoL-enabled hosts, sniff other users' WoL packets to harvest MACs and hex SecureOn passwords, and sweep for Apple devices to wake in bulk.

*No corpus evidence — derived from upstream documentation.*

## What it's for

WoL lets administrators power machines on remotely with a "magic packet"; wol-e (by Nathaniel Carew, v2.0, built on scapy/pcapy) turns that administrative feature into an assessment surface. It can wake a single host, brute-force MAC address ranges to find machines that respond to wake requests, sniff the local segment for other people's WoL traffic to collect targets and their SecureOn passwords, and ARP-scan for Apple devices (which shipped with WoL enabled by default) to wake them all at once. It does not exploit anything by itself — the value is in powering hosts on covertly and in the MAC/password intelligence gathered from the wire. Legacy code: it requires a Python 2 runtime and the 2012-era WoL ecosystem it targets.

## Typical use cases

### Power on a single host

```
wol-e -m 00:1A:2B:3C:4D:5E
```

Outcome: builds the magic packet (six `FF` bytes followed by the target MAC repeated 16 times, optionally a trailing hex password) and sends it as a UDP broadcast to `255.255.255.255:9`, printing the MAC, broadcast address, port and password used. Direct it at a specific subnet broadcast, port, or a sniffed SecureOn password with `-b 198.51.100.255 -p 9 -k 00:11:22:33:44:55` (the parser reads the values positionally, so keep that flag order).

### Sniff WoL requests and passwords

```
wol-e -s -i eth0
```

Outcome: captures WoL broadcasts on the given interface, printing each detected wake request (MAC, and password in hex when present) on screen and appending it to `WOLClients.txt` in the working directory — a passive way to collect target MACs and credentials that feed the single-host wake above.

### Find Apple devices to wake

```
wol-e -f
```

Outcome: repairs the local ARP cache, ARP-scans every attached network, matches responders against the bundled Apple OUI list and writes discovered Apple MACs to `AppleTargets.txt`; a follow-up `wol-e -fa` then sends one wake packet to every listed host and reports how many were attempted.

### Brute-force a MAC range

```
wol-e -a -p 9
```

Outcome: reads 4-byte MAC prefixes (format `00:12:34:56`, one per line) from `bfmac.lst` and cycles the low two octets of each (`00:12:34:56:00:00` through `00:12:34:56:FF:FF`), sending a wake packet per address; any host that powers on reveals itself as live and WoL-enabled.
