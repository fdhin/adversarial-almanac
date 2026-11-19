---
name: "libparistraceroute"
categories:
  - blackarch-networking
version: "380.455f5ef"
homepage: "https://github.com/libparistraceroute/libparistraceroute"
packaged-by: [blackarch]
blackarch-builddate: "2025-02-23"
build-system: autotools
license: LGPL
meta-checked: "2026-11-09"
github-repo: "libparistraceroute/libparistraceroute"
github-stars: 121
github-pushed: "2025-11-06"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# libparistraceroute

> C library for active network measurements, shipping load-balance-aware `paris-traceroute` and `paris-ping` tools on top.

## What it's for

Paris traceroute fixes the classic traceroute failure on per-flow load-balanced networks: classic traceroute varies the fields routers hash on between probes, so its "single path" is really a mixture of several paths. The libparistraceroute project (the current, maintained C implementation) keeps the flow-identifying header fields constant so every probe of a run follows the same path, and adds MDA — Multipath Discovery Algorithm — to enumerate the actual graph of parallel paths between two hosts. The package provides the library plus two tools: `paris-traceroute` (traceroute replacement, `-a mda` for multipath tracing) and `paris-ping` (the "Tokyo ping" latency measurement). Use it whenever path accuracy through load-balancing routers matters — anycast verification, transit comparison, middlebox placement.

## Typical use cases

### Trace one true path through load-balanced routers

```
paris-traceroute example.com
```

Outcome: prints a classic hop-by-hop traceroute, but every probe carries identical flow-identifying header fields so all probes follow the same path — no artificial "extra hops" from per-flow load balancers hashing different probes to different next hops.

### Enumerate every parallel path with MDA

```
paris-traceroute -a mda example.com
```

Outcome: runs the Multipath Discovery Algorithm, actively varying the flow at each hop to discover branching points, and reports the full graph of paths (where flows split and reconverge) instead of a single line.

### Force the probe protocol

```
paris-traceroute -T example.com
```

Outcome: sends TCP probes instead of the default UDP (`-U`), or ICMP with `-I`; `-4`/`-6` select the address family. Some middleboxes and firewalls answer only one probe protocol, so this changes which hops are visible.

### Measure per-path latency like Tokyo ping

```
paris-ping example.com
```

Outcome: runs the Paris-ping/"Tokyo ping" technique that keeps each measurement tied to a stable path through load balancers, producing round-trip latency figures that are comparable across runs rather than averaged over randomly shifting paths.

*No corpus evidence — derived from upstream documentation.*
