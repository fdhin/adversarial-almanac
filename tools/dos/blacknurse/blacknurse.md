---
name: "blacknurse"
categories:
  - blackarch-dos
version: "12.934b6a2"
homepage: "https://github.com/jedisct1/blacknurse"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-21"
build-system: make
license: BSD2
meta-checked: "2026-11-09"
github-repo: "jedisct1/blacknurse"
github-stars: 172
github-pushed: "2026-01-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# blacknurse

> Minimal C proof-of-concept for the BlackNurse attack: a low-bandwidth ICMP flood that pegs firewall CPUs.

## What it's for

BlackNurse is a denial-of-service technique in which a modest stream of ICMP "destination unreachable" (type 3, code 3) packets drives the CPU of certain firewalls to 100%, dropping legitimate traffic — documented against Cisco ASA, Palo Alto, SonicWall, Fortinet, Zyxel and others. This tool is Frank Denis' minimal C implementation of that attack: give it a single target address and it emits the offending ICMP packets from a raw socket. There are no options, no rate limiting and no reporting. It exists so administrators can reproduce the condition against equipment they own and verify that mitigations (ICMP type-3 rate limiting, geo-based filtering) actually work before a real event.

### Reproduce the BlackNurse condition against a firewall you own

One command, one target — everything else is fixed by design.

```
sudo blacknurse 198.51.100.10
```

Outcome: sends a continuous stream of ICMP type 3 / code 3 packets toward 198.51.100.10 until interrupted. On a vulnerable firewall in front of the target, control-plane CPU utilization climbs toward 100% and forwarded traffic starts degrading; on the attacking host, nothing is printed and no feedback is given — pair it with the firewall's own CPU metrics to judge the effect. Requires root (raw socket).

*No corpus evidence — derived from upstream documentation.*
