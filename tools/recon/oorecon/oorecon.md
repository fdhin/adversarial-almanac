---
name: "oorecon"
categories:
  - recon
version: "git (2026-09-28)"
homepage: "https://github.com/hilmiazizi/oorecon"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "hilmiazizi/oorecon"
github-stars: 9
github-pushed: "2026-09-28"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 13
gh-activity-checked: "2026-11-09"
worth: 5.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# oorecon

> Textual terminal dashboard that turns one apex domain into DNS, subdomain, port, reverse-IP and leaked-login-URL recon, then drills into per-host sitemaps, directories and JavaScript secrets.

## What it's for

oORecon is a Python Textual TUI for web reconnaissance and attack-surface discovery, aimed at bug bounty, pentest prep and OSINT inventory work. Instead of chaining separate CLIs, you give it a single domain (a pasted URL is normalized to the apex automatically) and it runs DNS record resolution, certificate-transparency subdomain enumeration with HTTPS liveness checks, a broad port scan, reverse-IP lookup against the apex's A/AAAA addresses, and a read-only listing of leaked login/credential URLs tied to the domain. From any live host you can continue into a host workspace: a robots.txt/XML sitemap crawler, directory brute-force with a SecLists-style wordlist, and a JavaScript miner that extracts endpoints/URLs and flags likely API keys and secrets with surrounding context for manual verification. It needs Python 3.10+ and, per its own disclaimer, authorization for everything you point it at — the port scanner and directory brute-force are active touches on the target. It does not attempt to replace specialist tools like amass, subfinder or ffuf; it packages a comparable workflow in one screen.

## Typical use cases

### Launch the recon dashboard for a domain

Start the TUI against an authorized apex domain.

```
python main.py example.com
```

Outcome: the dashboard opens and runs the parallel phases (DNS board, CT subdomains with HTTPS status filters, port list with service detail, reverse-IP hosts, leaked login URLs); `Ctrl+Q` quits, `/` filters the subdomain table, `Esc` navigates back.

### Open a host workspace and mine its JavaScript

From the subdomain tab, press Enter on a live host, then run the sitemap crawler, directory brute-force and JS miner.

```
python main.py example.com
# in the TUI: move to a live subdomain, press Enter, run the host pipeline
```

Outcome: the workspace shows the host's robots/sitemap endpoints, discovered directories, and JS-derived endpoint/URL lists; secret-like findings open a detail pane with the match and neighboring source text so you can judge false positives yourself — all within the same session, with per-host results cached until you quit.

> No corpus evidence — derived from upstream documentation.
