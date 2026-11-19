---
name: "ip-tracer"
categories:
  - blackarch-recon
version: "92.6b9c454"
homepage: "https://github.com/Rajkumrdusad/IP-Tracer"
packaged-by: [blackarch]
blackarch-builddate: "2024-09-21"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Rajkumrdusad/IP-Tracer"
github-stars: 3062
github-pushed: "2024-09-21"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ip-tracer

> CLI that reports geolocation, ISP and network details for an IP address via the ip-api.com service.

## What it's for

IP-Tracer (Rajkumar Dusad) is a small Bash/PHP command-line utility that looks up an IP address against the free ip-api.com geolocation API and prints a formatted report — country, city, coordinates, ISP/ASN, timezone and related fields. It can inspect your own public address or any address you name. It performs no scanning or OSINT beyond what the API returns, and the project itself declares it is no longer actively maintained.

## Typical use cases

### Look up your own public IP

See how your egress address is geolocated.

```
ip-tracer -m
```

Outcome: resolves the local public address and prints its full ip-api.com report (location, ISP, ASN and related fields).

### Look up a target address

Retrieve the network ownership and location of any IP.

```
ip-tracer -t 198.51.100.10
```

Outcome: prints the geolocation and ISP report for 198.51.100.10; the upstream installer also exposes the same entry point under the name `trace` (`trace -t 198.51.100.10`).

*No corpus evidence — derived from upstream documentation.*
