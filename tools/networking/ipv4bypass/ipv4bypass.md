---
name: "ipv4bypass"
categories:
  - blackarch-networking
version: "26.844d2d8"
homepage: "https://github.com/milo2012/ipv4Bypass"
packaged-by: [blackarch]
blackarch-builddate: "2025-09-07"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "milo2012/ipv4Bypass"
github-stars: 93
github-pushed: "2026-09-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ipv4bypass

> Enumerates the IPv6 addresses behind hosts discovered on the local IPv4 network and port-scans them over IPv6 — exposing services that IPv4 firewall rules and ACLs never cover.

## What it's for

ipv4Bypass (by milo2012) automates the "using IPv6 to bypass security" technique: hosts running dual stacks are often protected only at the IPv4 layer, so firewalls, ACLs and segmentation rules written for IPv4 do not apply to their IPv6 addresses. The tool takes a local network range, finds live hosts via arp-scan, enumerates their IPv6 addresses and port-scans over IPv6, reporting services reachable through the unfiltered IPv6 path. Use it in internal and segmentation assessments to demonstrate that IPv4-only controls don't hold on dual-stacked reality; it discovers and compares exposure but does not itself tunnel or exploit anything — acting on the findings is up to you.

## Typical use cases

### Audit a local subnet for IPv6 exposure

Check every dual-stack host on the LAN.

```
sudo ipv4bypass -i eth0 -r 198.51.100.0/24
```

Outcome: scans the /24 over IPv4 to find live hosts, enumerates their IPv6 addresses and open ports over IPv6, and prints the hosts and services reachable via IPv6 — candidates for bypassing the IPv4-layer security controls. `-h` lists the two options (`-i` interface, `-r` range).

*No corpus evidence — derived from upstream documentation.*
