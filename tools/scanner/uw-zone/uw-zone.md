---
name: "uw-zone"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://uberwall.org/bin/download/43/UWzone.tgz"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# uw-zone

> Multithreaded, randomized reverse zoner — walks an IP range with shuffled, time-jittered PTR lookups.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Reverse-DNS enumeration across a block of addresses: it resolves PTR records for the IPs in a range, but randomizes both the query order and the spacing between thread groups, so the lookup pattern does not read as a sequential zone sweep to anyone watching nameserver logs. You choose the resolver with `-N`. Written in C by coma of the UberWall team (2003–2005), packaged by BlackArch as `uw-zone`; usage verified against the upstream source (the original uberwall.org distribution is offline, but identical source circulates in public mirrors). Output is the hostnames found as the scan progresses — a reconnaissance primitive for mapping what lives in a range.

## Typical use cases

### Reverse-resolve a range through a chosen nameserver

```
uw-zone -S 198.51.100.1 -T 198.51.100.254 -N ns.example.com
```

Outcome: randomized multithreaded PTR lookups across the range via ns.example.com; discovered hostnames print as the scan runs.

### Blend the timing further

```
uw-zone -S 198.51.100.1 -T 198.51.100.254 -n 4 -t 5
```

Outcome: four threads spaced by a randomized minimum gap (the offset value plus or minus ~10 seconds per group), so queries land in an even less pattern-like rhythm; `-t 0` disables the timing randomization entirely for maximum speed.
