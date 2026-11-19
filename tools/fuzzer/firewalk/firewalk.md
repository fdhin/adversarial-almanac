---
name: "firewalk"
categories:
  - blackarch-fuzzer
version: "5.0"
homepage: "http://packetfactory.openwall.net/projects/firewalk/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-09-30"
build-system: autotools
license: BSD-2-Clause
meta-checked: "2026-11-09"
github-repo: "Seabreg/Firewalk"
github-stars: 0
github-pushed: "2019-09-14"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# firewalk

> Classic active-reconnaissance tool by Mike D. Schiffman (Packetfactory, 1998–2002) that maps which layer-4 protocols and ports an IP gateway will forward — despite this vault category, it is not a fuzzer.

*GitHub home: https://github.com/Seabreg/Firewalk — third-party mirror of the Packetfactory Firewalk 5.0 distribution; build files and the src/, include/ and man/ trees verified byte-identical (the source directories are carried inside the repo's ExtractFirst.7z).*

*No corpus evidence — derived from upstream documentation.*

## What it's for

Firewalk answers "what does this gateway or firewall let through?" It sends TCP or UDP probes with a TTL set one hop beyond the target gateway: if the gateway forwards a probe, the next-hop router expires it and returns ICMP TIME_EXCEEDED; if the traffic is filtered, nothing comes back. The tool first ramps hop counts traceroute-style until it is "bound" to the gateway, then scans. The second positional argument (the metric host) only has to be somewhere downstream of the gateway — it is never contacted directly. Use it to audit router/firewall ACLs from the outside; it sends no malformed payloads and tests no hosts.

## Typical use cases

### Map the default port set through a gateway

Determine which of the default probed ports the gateway forwards.

```
firewalk 198.51.100.1 198.51.100.10
```

Outcome: firewalk ramps its TTL until it binds to gateway 198.51.100.1, then probes the default port list (1-130, 139, 1025) toward the metric host, reporting each port as accepted (an ICMP time-exceeded came back) or filtered (no response).

### Check specific services with a TCP scan

Audit the ACL for exactly the services in scope.

```
firewalk -p TCP -S 22,80,443 198.51.100.1 198.51.100.10
```

Outcome: the same two-phase scan runs with TCP probes restricted to the listed ports (`-S` accepts ranges like 1-65535 and comma-separated lists).

### Skip discovery when the hop count is known

Shorten the ramping phase on a gateway whose distance is known.

```
firewalk -n -t 2 -T 5 198.51.100.1 198.51.100.10
```

Outcome: DNS resolution is disabled (`-n`), the initial TTL is preloaded to 2 (`-t`) for a gateway known to be two hops away, and each response is waited on for up to 5 seconds (`-T`, default 2) on slow links.

### Throttle a full-port sweep

Keep a broad scan from being dropped as a flood.

```
firewalk -P 500 -S 1-65535 198.51.100.1 198.51.100.10
```

Outcome: a 500 ms pause (`-P`) is inserted between written packets during the full-port sweep, trading speed for reliability on touchy gateways and intermediate IDS.
