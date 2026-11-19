---
name: "0trace"
categories:
  - blackarch-scanner
version: "1.5"
homepage: "http://jon.oberheide.org/0trace/"
packaged-by: [blackarch, kali]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "BlackArch/0trace"
github-stars: 12
github-pushed: "2014-03-04"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# 0trace

> Traceroute that rides inside an established TCP connection — hop enumeration that stateful firewalls let through because the probes belong to a session they already approved.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Discovering the real network path to a target when ordinary traceroute fails. Classic traceroute sends fresh ICMP/UDP/TCP packets with rising TTLs, which stateful firewalls and load balancers routinely drop or mangle, producing blank or fictional hops. 0trace instead watches a legitimate TCP session you open to the target, waits for a quiet moment in that conversation, and injects its TTL-limited probes as if they were part of that same connection — traffic the firewall classifies as belonging to an existing flow. The result is the genuine hop list to the service. It is a reconnaissance read-out, not an attack: it reveals topology, nothing more.

## Typical use cases

### Trace the path to a web service through a stateful firewall

Terminal 1 — open a TCP session and keep it alive:

```
telnet target.example.com 80
```

Terminal 2 — attach 0trace to that traffic:

```
sudo ./0trace.py eth0 TARGET_IP 80
```

Back in terminal 1 — generate some activity:

```
GET / HTTP/1.0
```

Outcome: 0trace reports it has acquired the target connection, waits for a gap in the traffic, then sends its probes. It prints a `TRACE RESULTS` table of numbered hops (starting at your gateway and ending with `Target reached.`) — the path as the firewall sees the approved session. Any hop that appears here but not in a normal traceroute is a device filtering stray probe packets.
