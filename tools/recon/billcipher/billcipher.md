---
name: "billcipher"
categories:
  - blackarch-recon
version: "32.97fba59"
homepage: "https://github.com/GitHackTools/BillCipher"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-03"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "GitHackTools/BillCipher"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# billcipher

> Menu-driven all-in-one lookup for a website or IP: whois, DNS, geoip, ports, headers and more.

## What it's for

BillCipher (GitHackTools) is an interactive information-gathering script: you launch it, enter a website or IP, and pick lookups from its menu — whois, DNS and subnet lookups, zone transfer attempt, GeoIP, reverse IP, shared DNS servers, port scan, HTTP headers, robots.txt, page links, host finding, email gathering (via Infoga), subdomain listing (via Sublist3r), admin-login finder (via Breacher), CloudFlare check/bypass (via HatCloud), website copying (via httrack) and host fingerprinting (via WhatWeb). It glues existing utilities and the HackerTarget API together rather than implementing recon itself, and needs both Python 2 and 3 components plus Ruby for some parts. The upstream repository is gone; this documents the final v2.3 behavior.

## Typical use cases

### Run the interactive lookup menu

```
billcipher
```

Outcome: prompts for a website or IP address, then presents the numbered feature menu; each selection prints that lookup's results to the terminal (whois record, DNS records, GeoIP location, open ports, and so on) — a single prompt-driven replacement for a dozen manual lookups on a target.

*No corpus evidence — derived from upstream documentation (archived project snapshot; upstream repository deleted).*
