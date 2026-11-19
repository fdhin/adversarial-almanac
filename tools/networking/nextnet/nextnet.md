---
name: "nextnet"
categories:
  - blackarch-networking
version: "3.c8dc7a6"
homepage: "https://github.com/hdm/nextnet"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: BSD
meta-checked: "2026-11-09"
github-repo: "hdm/nextnet"
github-stars: 449
github-pushed: "2025-08-04"
github-archived: false
github-issues: 13
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nextnet

> Pivot point discovery: sweeps networks for multi-homed hosts and reports every address they can reach.

## What it's for

nextnet (by HD Moore) scans CIDR ranges or single IPs for potential pivot points — machines visible on more than one network, such as multi-homed desktops, hypervisors or routers, which make useful jumping-off spots after an initial foothold. It fires lightweight UDP probes (notably NetBIOS name service on port 137) and prints one JSON object per responding host, including the host's name and — the key signal — a `nets` array of all IP addresses the host reports beyond the one that answered. It is discovery only: it finds and characterizes pivot candidates but does not tunnel, proxy or authenticate through them.

## Typical use cases

### Identify multi-homed hosts on a local subnet

Sweep a local /24 for machines answering NetBIOS probes.

```
nextnet 198.51.100.0/24
```

Outcome: one JSON line per responding host, e.g. `{"host":"198.51.100.112","port":"137","proto":"udp","probe":"netbios","name":"DESKTOP-...","nets":["198.51.100.112","203.0.113.20"],...}` — entries whose `nets` array contains addresses outside the scanned range are the pivot candidates.

### High-rate sweep of a large range

Raise the packet rate for a /16-scale sweep and keep only the pivot lines.

```
nextnet -rate 10000 198.51.100.0/16 | grep nets
```

Outcome: probes at up to 10000 packets per second (default is 1000) and filters the JSON stream down to lines containing discovered addresses, ready for jq or a file.

### Check a single suspected pivot host

Probe one address instead of a range.

```
nextnet 198.51.100.10
```

Outcome: if the host answers, a single JSON record with its name and full `nets` list is printed; an empty result means the host did not respond to the probes.

*No corpus evidence — derived from upstream documentation.*
