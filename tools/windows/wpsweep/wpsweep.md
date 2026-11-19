---
name: "wpsweep"
categories:
  - blackarch-windows
version: "1.0"
homepage: "https://vidstromlabs.com/freetools/wpsweep/"
packaged-by: [blackarch]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# wpsweep

> Minimal Windows ping sweeper: ICMP a range of addresses and list which ones answer.

## What it's for

WPSweep (Arne Vidstrom / Vidstrom Labs, v1.0) is a tiny Windows command-line ping sweeper — it walks a range of IP addresses with ICMP echo requests and prints the ones that reply. That is its entire job, and it is the quick "what's alive" check on a Windows box where you want zero dependencies: no Nmap, no PowerShell. It produces a live-host list only; there is no port scanning, OS detection or anything else. Targets are plain IP ranges (sweep large subnets with a sane timeout so the sweep does not outpace the link).

## Typical use cases

### Sweep a subnet for live hosts

Give it the first and last address of the range plus a per-probe timeout in milliseconds.

```
wpsweep 198.51.100.1 198.51.100.254 2000
```

Outcome: probes each address in the range with ICMP and prints the responding hosts as it goes — a plain alive list you can feed to follow-up tools; raise the trailing timeout value on slow or lossy links to avoid missing hosts.

*No corpus evidence — derived from upstream documentation (Vidstrom Labs tool page) and third-party usage references for the `start end timeout` argument form.*
