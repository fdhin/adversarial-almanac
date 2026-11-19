---
name: "forkingportscanner"
categories:
  - blackarch-scanner
version: "1"
homepage: "http://magikh0e.xyz/"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-18"
build-system: perl
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# forkingportscanner

> magikh0e's minimal Perl TCP/UDP port scanner: one host per run, speed coming from forking a worker per slice of the port range rather than from raw packets.

*No corpus evidence — derived from upstream documentation. No verifiable CLI — the upstream script's only distribution point (magikh0e.xyz) is gone and was never archived, so no option syntax can be verified against the source; this note is description-only.*

## What it's for

ForkingPortScanner is a small, deliberately simple Perl scanner from the pre-nmap-everywhere era. It scans a single host at a time and gets its parallelism by forking across the requested port range (1-65535 by default), so a full-range sweep spawns many short-lived connection attempts instead of one long sequential walk. It speaks TCP by default and can be switched to UDP. There is no service detection, no timing template, no output format beyond the terminal — connect, report open ports, done. Its niche was a dependency-light sweep where installing a fuller toolkit wasn't possible; today nmap, masscan or any of the async scanners cover the same ground with far better information, so this tool is of practical interest mainly on hosts that already carry the package.
