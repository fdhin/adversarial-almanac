---
name: "pyminifakedns"
categories:
  - blackarch-networking
version: "0.1"
homepage: "http://code.activestate.com/recipes/491264/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "aur-archive/pyminifakedns"
github-stars: 0
github-pushed: "2015-08-17"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pyminifakedns

> Single-file DNS server that answers every A query with one fixed address.

## What it's for

pyminifakedns is a ~40-line Python 2 script published as an ActiveState code recipe ("Mini Fake DNS server", Francisco Santos). It binds UDP 53 and blindly answers the first question of any standard DNS query with a single IPv4 A record that you edit into the script — no zones, no recursion, no other record types. Its stated purpose is malware analysis on virtual machines: point a VM's resolver at it and every hostname resolves to your analysis host, exposing which domains the sample tries to contact. Administrative privileges are required to bind port 53, and the reply address is set by editing the `ip` variable in the script's `__main__` block (shipped as 192.168.1.1).

## Typical use cases

### Resolve every hostname to one analysis host

Answer all lookups from a lab VM with a controlled address.

```
# 1. edit the __main__ block of the script:  ip = '198.51.100.10'
# 2. run it as root (it binds port 53)
sudo pyminifakedns
```

Outcome: the script prints `pyminifakeDNS:: dom.query. 60 IN A 198.51.100.10`, binds UDP 53 and logs every answered query (`Respuesta: <name> -> <ip>`); set the VM's DNS server to this host and every name it asks for resolves to 198.51.100.10, so DNS callbacks in the analyzed software become visible in the log.

*No corpus evidence — derived from upstream documentation.*
