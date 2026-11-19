---
name: "uw-udpscan"
categories:
  - blackarch-scanner
version: "0.1"
homepage: "http://uberwall.org/bin/download/44/UWudpscan.tar.gz"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-06"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# uw-udpscan

> Multithreaded UDP port scanner from the UberWall team, with tunable thread count and timeout.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Straightforward UDP port scanning: give it a host and a port range and it fires UDP probes from a worker pool, reporting what answers. Written in C by coma of the UberWall team (2003–2005); BlackArch packages the "xmas release" as `uw-udpscan`, and the packaged README documents the flag set. UDP scanning is inherently indirect — open ports usually answer, closed ones trigger ICMP unreachable, filtered ones stay silent — so treat its output as a first pass to confirm with a fuller scanner. When you raise the thread count, raise the timeout too (the README's own advice), or replies still in flight get dropped.

## Typical use cases

### Scan the low ports of a host

```
uw-udpscan -h 198.51.100.10 -l 1 -u 1024
```

Outcome: multithreaded UDP scan of ports 1–1024 with the default timeout and thread count, printing per-port results as they resolve.

### Push throughput and keep the results

```
uw-udpscan -h 198.51.100.10 -l 1 -u 65535 -n 30 -t 10 -f results.txt
```

Outcome: 30 scanning threads with a 10-second timeout walk the full port range and write the findings to results.txt instead of only printing them.
