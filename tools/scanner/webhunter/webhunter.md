---
name: "webhunter"
categories:
  - blackarch-scanner
version: "12.918b606"
homepage: "https://github.com/peedcorp/WebHunter"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "peedcorp/WebHunter"
github-stars: 5
github-pushed: "2018-01-28"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# webhunter

> Menu-driven Python 2 console that chains DNS recon, IP intelligence, admin-panel brute forcing and footprinting in one interactive session.

## What it's for

WebHunter (peedcorp, 2018) is an interactive recon assistant: instead of memorizing a dozen single-purpose tools, you launch it and pick from numbered menus. It bundles DNS analysis (lookups, DNS reports, shared-nameserver pivoting, propagation checks, Cloudflare-bound subdomain discovery, link/tag extraction), IP intelligence (reverse-IP site listing, geolocation, IP history, traceroute), whois, footprinting, search-engine queries, login-panel brute forcing and a one-shot "scan automatic" mode. Several functions lean on public lookup APIs (ViewDNS-style), so it is a client for those services as much as a scanner. It is Linux/macOS-only Python 2 with no CLI flags — everything happens through the prompts after launch — and it needs a network connection to those third-party APIs to do anything useful.

## Typical use cases

### Launch the console and run the all-in-one scan

```
python2 WebHunter.py
```

Outcome: prints the ASCII banner and the main menu; entering `7` (scan automatic) runs the tool's full chained workflow against a target you type at the prompt, combining its DNS, IP and footprinting steps in sequence.

### Work through DNS reconnaissance interactively

```
python2 WebHunter.py
```

Outcome: choosing `1` (dns analysis) opens the DNS submenu — options include DNS record lookup, an image-format domain map, a full DNS report, shared-DNS-server target discovery, propagation checks, forward-record enumeration, Cloudflare subdomain hunting, link extraction and page-tag scraping; `back` returns to the main menu and results print to the console as each option finishes.

### Pivot from an IP to co-hosted sites and history

```
python2 WebHunter.py
```

Outcome: choosing `2` (finger ip) or `4` (who's) opens the IP/whois submenu — reverse-IP lists every site on the server, whois-IP geolocates it, IP history shows where a domain used to point, and the trace options map the network path; use these to expand a single known IP into a target list.

*No corpus evidence — derived from upstream documentation.*
