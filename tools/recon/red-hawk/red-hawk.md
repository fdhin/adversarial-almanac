---
name: "red-hawk"
categories:
  - blackarch-recon
version: "36.fa54e23"
homepage: "https://github.com/Tuhinshubhra/RED_HAWK"
packaged-by: [blackarch]
blackarch-builddate: "2020-09-18"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Tuhinshubhra/RED_HAWK"
github-stars: 3812
github-pushed: "2024-07-31"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# red-hawk

> Menu-driven PHP console that bundles a dozen information-gathering and vulnerability scans (whois, geo-IP, DNS, subdomains, reverse IP, SQLi, WordPress) behind one interactive prompt.

## What it's for

RED HAWK is an all-in-one interactive scanner written in PHP: launch it, type a domain, answer whether it runs HTTPS, then pick a scan from the menu — basic scan (title, IP, web server, CMS, Cloudflare, robots.txt), whois lookup, geo-IP, banner grabbing, DNS lookup, subnet calculator, Nmap port scan, subdomain scanner, reverse IP lookup with CMS detection, error-based SQLi scanning, "bloggers view" (Alexa/DA/PA/social links — needs moz.com API keys configured in `config.php`), WordPress audit (sensitive files, version and version-based vulnerabilities), crawler and MX lookup, or "scan for everything". It detects WordPress, Joomla, Drupal and Magento sites. It is single-target and menu-driven — no CLI flags, no batching — and its strength is breadth-on-tap rather than depth.

## Typical use cases

### Start an interactive scan session

```
php rhawk.php
```

Outcome: opens the RED HAWK console; enter a bare domain (no `http://` prefix), choose HTTP or HTTPS, then select a scan type from the menu (e.g. basic scan, whois, sub-domain scanner, WordPress scan or "scan for everything") and the formatted results print to the terminal. Type `help` to list all available commands.

### Install missing PHP dependencies from inside the tool

```
php rhawk.php
fix
```

Outcome: the built-in `fix` command installs the required PHP modules (curl, xml, etc.) that RED HAWK depends on — the documented remedy when the scanner dies right after Cloudflare detection.

*No corpus evidence — derived from upstream documentation.*
