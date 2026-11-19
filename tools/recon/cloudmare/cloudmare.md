---
name: "cloudmare"
categories:
  - blackarch-recon
version: "108.9c5a39f"
homepage: "https://github.com/MrH0wl/Cloudmare"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "MrH0wl/Cloudmare"
github-stars: 1784
github-pushed: "2023-07-25"
github-archived: true
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# cloudmare

> Combine subdomain enumeration, nameserver records, Censys/Shodan/SecurityTrails searches and Host-header probes to find origins behind Cloudflare, Sucuri or Incapsula.

## What it's for

Cloudmare hunts the origin server of sites behind Cloudflare (and Sucuri, Incapsula) by exploiting misconfigured DNS. It assembles candidate hosts and IPs — subdomains via an embedded Sublister engine, DNS nameservers, plus Censys, Shodan and SecurityTrails searches keyed from `data/APIs/api.conf` — then probes each candidate with Host-header requests, netcat checks and DNS lookups to see whether it serves the target site directly. Runs on Python 3.7+; the upstream repository is archived, but the code is self-contained.

## Typical use cases

### Full origin sweep

Throw every technique at one target.

```
cloudmare -u example.com --bruter -sC -sSh -sSt --host verified.example.com
```

Outcome: enumerates subdomains, adds nameserver records and Censys/Shodan/SecurityTrails results (API keys from api.conf), then probes every candidate — including the known-good host — for direct responses; candidates that answer for the target domain outside the CDN are the exposed origin.

### Brute-force subdomains only

Skip the search APIs and work the DNS angle.

```
cloudmare -u example.com --subbruter
```

Outcome: enables the subbrute module to brute-force subdomain names on top of passive enumeration, checking each resolution for records that bypass the CDN.

### Verify a single candidate

Test one suspected origin you already have.

```
cloudmare -u example.com --host direct.example.com
```

Outcome: runs an ISP check that rejects known CDN ranges, then probes only the supplied host for serving the target domain — a fast confirmation of a lead from elsewhere.

### Read the advanced help

See every option group.

```
cloudmare -hh
```

Outcome: prints the advanced help (`--help-hack`), documenting the target, request, search and output option groups beyond the basic `-h` screen.

*No corpus evidence — derived from upstream documentation and source (lib/utils/cmdline.py).*
