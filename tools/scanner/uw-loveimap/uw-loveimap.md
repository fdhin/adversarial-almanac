---
name: "uw-loveimap"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://uberwall.org/bin/download/45/UWloveimap.tgz"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# uw-loveimap

> Multithreaded IMAP bounce scanner — uses an authenticated mail server as a launch point to port-scan another host.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Port-scanning a target from a mail server's vantage point. You feed it working IMAP credentials; it logs into the IMAP server and uses that session to probe ports on a third host, so the scan traffic originates from (and is attributed to) the mail server rather than your box — useful when the mail host can reach network segments you cannot. Written in C by coma of the UberWall team (2003–2005), packaged by BlackArch as `uw-loveimap`; usage verified against the upstream source. It needs valid IMAP credentials before it can do anything, and single-threaded runs are the stealthiest option because they open the fewest connections to the mail server.

## Typical use cases

### Bounce-scan a host through an IMAP server

```
uw-loveimap -u user -p Password1 -h imap.example.com -t 198.51.100.10 -P 21,22,25,80-110
```

Outcome: authenticates to imap.example.com and probes the listed ports (single ports, comma lists, and ranges all accepted) on the target from the mail server, reporting per-port results as the scanning threads complete.

### Trade speed for stealth

```
uw-loveimap -u user -p Password1 -h imap.example.com -t 198.51.100.10 -P 1-1024 -n 1
```

Outcome: runs the port range single-threaded — the lowest number of IMAP connections, hence the stealthiest and slowest profile; raising `-n` toward the 256-thread ceiling speeds the scan at the cost of a noisier pattern on the mail server.
