---
name: "lft"
categories:
  - blackarch-recon
version: "3.91"
homepage: "http://pwhois.org/lft/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-05-04"
build-system: autotools
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Seabreg/lft"
github-stars: 0
github-pushed: "2019-05-19"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lft

> Layer Four Traceroute — path discovery that probes with TCP/UDP/ICMP instead of plain ICMP, so it maps paths through firewalls that swallow ordinary traceroutes.

## What it's for

LFT (Layer Four Traceroute, maintained since 1998 by Victor Oppleman at the Prefix WhoIs Project, pwhois.org) sends transport-layer probes — by default TCP SYN to port 443 — rather than the ICMP/UDP packets classic traceroute uses, which lets it trace past filters that drop traceroute traffic. Alternative engines are one flag away: `-F` FIN probes, `-E` adaptive (cycles FIN/SYN-ACK/SYN and labels what it meets — stateful inspector, flag filter, broken stack), `-u` UDP, `-p` ICMP, `-P` RFC 1393. Each trace ends with a verdict (`[target open]`, `[target closed]`, `[target filtered]`), and hops can be attributed to their origin AS and network name straight from the live routing table. The same tarball ships `whob`, a companion one-line whois. BlackArch packages 3.91; upstream's current 4.x line adds IPv6 end-to-end, ECMP enumeration, JSON/GraphViz output and a watch mode — flag semantics below follow current upstream docs.

## Typical use cases

### Trace the path to a host with TCP probes

```
lft target.example.com
```

Outcome: a hop-by-hop trace using the default TCP SYN engine (port 443 in current releases), printing TTL, hop address and timing per hop; unanswered stretches are reported as `[neglected]` and the trace ends with a verdict on the target port such as `[target open]`.

### Trace to a specific service port adaptively

```
lft -E target.example.com:8080
```

Outcome: cycles FIN, SYN-ACK and SYN probes against port 8080, annotating gateways that appear to statefully inspect or filter — use it when the default engine's packets are eaten and the path to a particular service matters.

### Attribute every hop to its network

```
lft -A -N target.example.com
```

Outcome: every hop is annotated with the origin AS (resolved live from the global routing table via Prefix WhoIs) and, with `-N`, the network name — turning a raw IP list into a provider-boundary map of the path.

*No corpus evidence — derived from upstream documentation (pwhois.org/lft, current release); packaged license verified from the 3.91 tarball COPYING (VOSTROM Public License for Open Source).*

GitHub: third-party mirror of the pwhois.org LFT source (Seabreg/lft, a v3.8 snapshot — README identical to upstream, the code differing from the packaged 3.91 only in its version string).
