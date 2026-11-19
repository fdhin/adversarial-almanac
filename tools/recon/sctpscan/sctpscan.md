---
name: "sctpscan"
categories:
  - blackarch-recon
version: "34.4d44706"
homepage: "http://www.p1sec.com/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2015-10-13"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "philpraxis/sctpscan"
github-stars: 94
github-pushed: "2021-09-17"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sctpscan

> SCTP-aware network scanner — finds hosts and ports that TCP/UDP-only scanners render invisible.

## What it's for

sctpscan probes networks for the Stream Control Transmission Protocol, which carries telecom signaling (SIGTRAN) traffic and is routinely forgotten in firewall policy, making SCTP services a soft spot on otherwise hardened hosts. It can sweep a subnet for SCTP-capable stacks, check individual host/port pairs, port-map the frequently used SCTP ports, fuzz remote SCTP stacks, and bridge TCP connections to an SCTP destination. Written by Philippe Langlois (P1 security). Local kernel SCTP support can skew scan results, so checksum behavior is selectable (-C crc32, -A adler32, -b both).

## Typical use cases

### Sweep a subnet for SCTP hosts

```
sctpscan -s -l 198.51.100.20 -r 198.51.100
```

Outcome: sends SCTP probes across the address prefix and reports each host where SCTP is present (e.g. "198.51.100.3 SCTP present on port 10000"), plus any INIT/ABORT chunks received back.

### Probe a single host and port

```
sctpscan -l 198.51.100.20 -r 198.51.100.10 -p 9999
```

Outcome: attempts an SCTP exchange with the target port from the given local address and reports whether an SCTP stack answers — the quick check before deeper enumeration.

### Scan only the frequently used SCTP ports

```
sctpscan -s -F -l 198.51.100.20 -r 198.51.100
```

Outcome: port-scans just the well-known SCTP ports on the subnet's hosts instead of the full 0-65535 range, trading completeness for speed.

### Stand up a test SCTP server

```
sctpscan -d
```

Outcome: starts a dummy SCTP server on port 10000 so scanning behavior can be validated from another machine (scanning your own host's kernel SCTP stack gives misleading results).

*No corpus evidence — derived from upstream source (usage text in sctpscan.c; repository identity confirmed by the BlackArch PKGBUILD source line).*
