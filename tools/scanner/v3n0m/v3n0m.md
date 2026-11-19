---
name: "v3n0m"
categories:
  - blackarch-scanner
version: "536.16b629f"
homepage: "https://github.com/v3n0m-Scanner/V3n0M-Scanner"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "v3n0m-Scanner/V3n0M-Scanner"
github-stars: 1579
github-pushed: "2023-11-14"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# v3n0m

> Menu-driven pentest console — dork-built target lists scanned for SQLi/XSS/LFI, mass vulnerable-IP hunting with Toxin, and Cloudflare origin discovery.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A single Python console (Python 3.6+) that bundles a decade of dork-scanning workflows behind numbered menus: search-engine dorking to harvest target sites, then scanning those lists for SQL injection, XSS and LFI (with dedicated vB5 and WordPress modules); "Toxin", a scanner that sweeps large blocks of random IPs for known-vulnerable services; Cloudbuster, which resolves the origin IP behind Cloudflare protection (CrimeFlare database, subdomain and admin-panel enumeration); DNS bruteforcing; WAF detection; admin-page finding; and SOCKS4/5 proxy support. Everything happens through interactive prompts — the main entry takes no command-line flags. GPLv3, cross-platform. The README warns that recent versions expect a proxy or VPN to be in use.

## Typical use cases

### Launch the console and work the menus

```
v3n0m
```

Outcome: prints the banner and numbered main menu; each option leads through interactive prompts (dorking asks for the site extension to search, the dork list, thread count and pages to pull; Toxin asks how many IPs to scan and with how many threads; proxy setup asks socks4 or socks5 plus host and port). Vulnerable findings accumulate in per-module result lists as each scan finishes.
