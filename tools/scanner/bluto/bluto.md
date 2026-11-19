---
name: "bluto"
categories:
  - blackarch-scanner
version: "142.25cad7a"
homepage: "https://github.com/darryllane/Bluto"
packaged-by: [blackarch]
blackarch-builddate: "2020-05-16"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "darryllane/Bluto"
github-stars: 670
github-pushed: "2022-09-24"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluto

> DNS recon in one run: subdomain brute forcing, zone-transfer attempts, wildcard detection, and email/staff enumeration.

## What it's for

Bluto (by Darryl Lane; the original RandomStorm repository has moved to darryllane/Bluto) chains several DNS reconnaissance steps against one domain: it pulls MX and NS records, gathers subdomains passively via NetCraft, attempts a zone transfer against every authoritative name server, detects wildcard DNS and — when wildcards are in place — falls back to a slower brute-force technique, then brute-forces subdomains in parallel from the Alexa top 1 million (top 20k by default, a much larger list with `-e`). It continues into email and staff enumeration via search engines, the EmailHunter API, and LinkedIn, checks the addresses found against haveibeenpwned, and writes an HTML evidence report. It is Python 2.7 software from the mid-2010s — expect legacy dependencies.

## Typical use cases

### Full recon of a domain

```
bluto -d example.com
```

Outcome: runs the whole chain (NS/MX, passive subdomains, AXFR attempts, wildcard-aware brute forcing, email enumeration) and produces on-screen results plus an HTML evidence report.

### Bigger subdomain wordlist

```
bluto -d example.com -e
```

Outcome: same run, but brute-forcing subdomains with a very large list instead of the default top-20k.

### Email enumeration with EmailHunter

```
bluto -d example.com -api <emailhunter_api_key>
```

Outcome: enriches the email/staff enumeration stage with EmailHunter data and checks every address found against haveibeenpwned; `-t 20` raises the per-request timeout above the default 10 seconds.

*No corpus evidence — derived from upstream documentation (darryllane/Bluto, the packaged upstream).*
