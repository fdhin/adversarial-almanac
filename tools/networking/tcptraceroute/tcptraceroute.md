---
name: "tcptraceroute"
categories:
  - blackarch-networking
version: "1.5beta7"
homepage: "http://michael.toren.net/code/tcptraceroute/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2019-09-08"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "mct/tcptraceroute"
github-stars: 134
github-pushed: "2021-10-13"
github-archived: false
github-issues: 11
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# tcptraceroute

> Traceroute that probes with TCP SYN packets (Michael C. Toren) — traces paths to services through firewalls that silently drop classic UDP/ICMP traceroutes.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Path discovery when traditional traceroute fails. Classic traceroute sends UDP or ICMP ECHO packets with increasing TTLs, which many firewalls filter; tcptraceroute instead sends TCP SYN packets to a destination port the target actually listens on (default 80), which firewalls commonly permit, and reads the ICMP time-exceeded replies hop by hop. It never completes a handshake: a SYN|ACK from the target means the port is open (the kernel then sends an RST), an RST means closed — the same half-open technique as `nmap -sS`. Reverse lookups are skipped for RFC1918 hops by default. It is a path/port-state probe, not a scanner or a full connection client.

## Typical use cases

### Trace the path to a web server

```
tcptraceroute target.example.com
```

Outcome: hop-by-hop listing of gateways generating ICMP time-exceeded replies, ending at the target (destination port 80 by default). A final `SYN|ACK` line means the traced port is open; `RST` means closed.

### Trace to a specific service port, no per-hop DNS

```
tcptraceroute -n target.example.com 443
```

Outcome: same hop-by-hop path but probing TCP 443, with numeric addresses only (`-n` skips the reverse DNS lookup for every hop — faster and less noisy).

### Trace through stateless firewalls with ACK probes

```
tcptraceroute -A target.example.com 22
```

Outcome: probes carry the ACK flag instead of SYN, so stateless firewalls that permit "established" outbound connections forward them; useful when SYN-based traces are dropped mid-path.

### Limit the hop range and probe count

```
tcptraceroute -f 5 -m 15 -q 1 target.example.com
```

Outcome: starts at TTL 5 (`-f`), stops at TTL 15 (`-m`, default 30) and sends a single probe per hop (`-q`, default 3) — a quick re-check of a known segment instead of a full 30-hop trace.
