---
name: "ripdc"
categories:
  - blackarch-recon
version: "0.4"
homepage: "http://nullsecurity.net/tools/scanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-23"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ripdc

> Minimal shell wrapper that maps all domains hosted on an IP or domain via a reverse-IP lookup API.

## What it's for

ripdc (Reverse IP Domain Checker, `ripdc.sh` by noptrix / nullsecurity.net) is a tiny POSIX-shell script that answers one question: which domains resolve to this IP? It takes a target, queries the hackertarget.com reverse-IP lookup API, and prints every co-hosted domain it returns — nothing more (no port checks, no CMS fingerprinting, no recursion). Because it rides a free public API, results are capped by that API's rate limits; when the limit is hit the script warns rather than failing silently. The same job with extra annotations (HTTP status, CMS) is done by `reverseip` (Ruby); the unrelated Python 2 tool `revipd` shared this niche but its upstream is gone.

## Typical use cases

### Reverse-map an IP address to its domains

```
ripdc -t 198.51.100.10
```

Outcome: queries the reverse-IP API for the address and prints each co-hosted domain on its own line under a `listing found domains` banner — a clean, script-consumable list for pivoting from one web host to its neighbors.

### Reverse-map a domain, verbosely

```
ripdc -t example.com -v
```

Outcome: same lookup starting from a hostname, with verbose mode (`-v`) surfacing the API request details and any error output normally discarded — useful when the run comes back empty and you need to distinguish "no domains" from "API limit exceeded".

*No corpus evidence — derived from upstream documentation (ripdc.sh usage/help text as packaged).*
