---
name: "wups"
categories:
  - blackarch-windows
version: "1.4"
homepage: "http://ntsecurity.nu/toolbox/wups/"
packaged-by: [blackarch]
blackarch-builddate: "2016-07-28"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wups

> WUPS — a small Windows GUI UDP port scanner where the inter-probe delay is the critical setting.

## What it's for

WUPS (Windows UDP Port scanner, Arne Vidstrom, ntsecurity.nu, v1.4) was written because every Windows port scanner of its era only handled TCP: it sends UDP probes to a target's port range and reports open ports. UDP scanning is inherently slower — the tool's Delay setting is the make-or-break parameter: set too low, the probe stream overwhelms the link and results become garbage; the target must be given as an IP address (no DNS names), the host's liveness is not checked first, and results appear only when the whole scan finishes. As a legacy Win9x/NT/2000/XP-era GUI tool it has no CLI flags and is mainly of use against old lab targets; on a modern box, `nmap -sU` does the same job with better throughput handling.

## Typical use cases

### UDP-scan a host from the GUI

Set target, ports and a conservative delay, then let it run to completion.

```
wups.exe
```

Outcome: the scanner window opens; enter the target by IP address, the port range, and a delay generous for the link, then start — WUPS probes the range and, once the scan completes, lists the UDP ports found open on the target. If nothing appears, first suspect too-aggressive delay settings rather than a closed host.

*No corpus evidence — derived from upstream documentation (ntsecurity.nu/Vidstrom Labs WUPS page).*
