---
name: "intrace"
categories:
  - blackarch-recon
version: "1.6"
homepage: "https://github.com/robertswiecki/intrace"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-11-30"
build-system: make
license: GPL-2.0-only, GPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "robertswiecki/intrace"
github-stars: 73
github-pushed: "2022-10-31"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# intrace

> Traceroute that piggybacks on an existing TCP connection instead of firing fresh probes.

## What it's for

InTrace (Robert Swiecki, 2007) enumerates the IP hops between you and a target by manipulating the TTL of packets belonging to a TCP connection that already exists — one you opened yourself or any established connection visible on your system — eliciting ICMP TTL-exceeded replies from each intermediate router. Because no new connection is opened toward the target, it can trace paths through filtering devices that block classic UDP/ICMP traceroutes and can expose NAT appliances and internal addressing near the destination. It is a path-discovery tool, not a scanner.

## Typical use cases

### Trace the path of a connection you initiate

Let intrace watch an interface and use a connection you open yourself.

```
intrace -i eth0 -h target.example.com
nc target.example.com 80
```

Outcome: intrace attaches to traffic toward the target and waits until it has acquired enough packets from your connection, then prompts you to press ENTER; from then on it prints the hop-by-hop path, each hop labeled `ICMP TTL-EXCEEDED`, `NO RESPONSE` or `TCP REPLY`, revealing routers and NAT devices a normal traceroute may not show.

### Trace a remotely initiated connection

Trace traffic for a connection someone else opened to your host.

```
netstat -tanp
intrace -i eth0 -h 198.51.100.10
```

Outcome: after picking the peer address of an established connection from the netstat listing, intrace traces the reverse path of that live connection — useful for discovering which hops and NAT appliances remote clients sit behind without generating any new sessions.

*No corpus evidence — derived from upstream documentation.*
