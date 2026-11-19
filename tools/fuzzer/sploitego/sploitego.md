---
name: "sploitego"
categories:
  - blackarch-fuzzer
version: "153.d9568dc"
homepage: "https://github.com/allfro/sploitego"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "allfro/sploitego"
github-stars: 397
github-pushed: "2019-10-15"
github-archived: false
github-issues: 14
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sploitego

> Sploitego — Maltego local transforms that turn pentest recon output into graph pivots, built on the Canari framework.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Sploitego (Nadeem Douba, first presented at DEF CON 20) is a package of local Maltego transforms built on the Canari framework: entities in a Maltego graph (DNS names, netblocks, hosts, ports, Metasploit data) pivot through transforms that wrap nmap, amap, p0f, scapy and other tools — DNS lookups across record types, subdomain enumeration, DNS cache snooping, GeoIP location, neighbour/resolver discovery, and Metasploit RPC-driven exploit and shell interaction. It also ships a Qt console for Metasploit (`qtmsfconsole`) and machine files that chain several transforms into a single pivot. It is Python 2-era software; the transforms execute locally and expect the external tools they wrap (nmap, p0f, amap, msfrpcd, ...) to be installed on the same host.

## Typical use cases

### Register the transforms in Maltego

Makes the package's transforms and entities available inside Maltego.

```
canari install-package sploitego
```

Outcome: installs sploitego's transform definitions and entity metadata into Maltego's local transform registry; after a Maltego restart the transforms appear on the Run Transform menu, so a DNS-name entity can be pivoted to subdomains, MX/NS/TXT records or GeoIP data.

### Drive Metasploit from the Qt console

Opens an interactive console against a running Metasploit RPC daemon.

```
qtmsfconsole 127.0.0.1 55553 /api 1
```

Outcome: connects to msfrpcd at 127.0.0.1:55553 (URI `/api`) and opens the Qt console attached to session 1, from which exploits and sessions can be driven next to the Maltego graph entities that reference them.
