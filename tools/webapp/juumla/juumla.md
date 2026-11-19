---
name: "juumla"
categories:
  - blackarch-webapp
version: "114.4c6f746"
homepage: "https://github.com/oppsec/juumla"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "oppsec/juumla"
github-stars: 177
github-pushed: "2026-04-10"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 8
gh-activity-checked: "2026-11-09"
---

# juumla

> Python scanner for Joomla sites: fingerprints the CMS, identifies its version, and reports known-vulnerable components and exposed config or backup files.

## What it's for

juumla is a lightweight recon tool specialized in Joomla CMS. Given one base URL it confirms the site actually runs Joomla, works out the CMS version, checks that version against its built-in vulnerability knowledge, and probes for sensitive files left reachable — configuration files and backups being the classic Joomla exposure. Reach for it during web enumeration the moment a target looks like Joomla, before deeper manual digging; it is deliberately narrow (no crawling, no exploitation, low CPU/RAM footprint) and its findings are the input to your own CVE matching. If it does not detect Joomla, there is nothing more it can do on that host.

## Typical use cases

### Fingerprint and scan a Joomla site

The single-target run.

```
juumla -u https://target.example.com
```

Outcome: prints whether Joomla was detected, the identified version, any components flagged as vulnerable for that version, and the sensitive/config/backup file paths found reachable on the target — a compact recon summary to feed into version/CVE research.

### Scan with custom headers

Reach targets behind authentication or header-based gating.

```
juumla -u https://target.example.com -H "Authorization: Bearer <token>" -H "X-Forwarded-For: 198.51.100.10"
```

Outcome: every `-H "Name: Value"` header (repeatable flag) is attached to all requests juumla makes, letting the scan run against staging hosts that require a session token or a specific source address; the report is identical to the plain run.

No corpus evidence — derived from upstream documentation (oppsec/juumla README plus argparse in `main.py`: `-u` required, `-H` repeatable).
