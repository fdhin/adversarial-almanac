---
name: "dhcpf"
categories:
  - blackarch-fingerprint
version: "3.a770b20"
homepage: "https://github.com/elceef/dhcpf"
packaged-by: [blackarch]
blackarch-builddate: "2016-08-20"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "elceef/dhcpf"
github-stars: 52
github-pushed: "2016-07-12"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dhcpf

> Passive DHCP client fingerprinter — sniffs DHCP requests on a network segment and maps each client's option combination to an operating system.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Identifying the operating system of every device that asks for a DHCP lease, without sending a single packet. dhcpf (C, by Marcin Ulikowski) passively watches DHCP client requests (UDP 68 to 67), extracts the unique combination of DHCP options and requested parameters, and matches it against a bundled signature database to name the client OS — e.g. "Android 2.2 (Motorola)", "Windows XP/Vista/7 (generic)", "Linksys SipuraSPA". Typical uses are asset inventory, rogue-device detection and network forensics on a monitor/SPAN port. It fingerprints DHCP clients, not servers, and its usefulness is bounded by the signature database (upstream explicitly asks for new signatures). Requires root and libpcap; the whole CLI is a single interface argument.

## Typical use cases

### Fingerprint DHCP clients on a live network

Identify the OS of every device requesting a lease on a segment.

```
sudo dhcpf eth0
```

Outcome: after loading its DHCP-print database, prints one block per observed Discover/Request — client MAC with vendor prefix, matched `system`, requested IP, hostname, and option-82 relay data when present; run it against a SPAN/monitor port for the segment of interest and watch new clients appear as they (re)join.
