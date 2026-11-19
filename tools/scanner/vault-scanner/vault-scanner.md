---
name: "vault-scanner"
categories:
  - blackarch-scanner
version: "299.0303cf4"
homepage: "https://github.com/abhisharma404/vault"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "abhisharma404/vault"
github-stars: 560
github-pushed: "2023-07-18"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vault-scanner

> "Swiss army knife for hackers" — one Python console spanning web scanning (XSS, LFI, fuzzing), TCP port/OS scans, and recon like dorking, whois and CMS detection.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A many-flagged Python 3 tool (the `vault-scanner` binary wraps `vault.py`) that packs a whole assessment's first-pass checks into one argparse surface. Web-side it gathers information, comments and emails, fuzzes URLs, checks SSL, hunts XSS and LFI, finds admin panels, detects CMSes and jQuery versions, and tests open redirects. Network-side it port-scans (connect plus FIN/NULL/ACK/XMAS variants), ping-sweeps, ARP-scans and guesses the operating system. Recon-side it runs Google dorks and whois lookups. Reach for it when you want one broad, quick pass over a target rather than a deep dive — it is breadth-first by design, and its individual modules do not replace dedicated tools.

## Typical use cases

### Run the README's canonical combined web scan

```
vault-scanner -u http://target.example.com -info -comment -ssl -fuzz
```

Outcome: runs information gathering, HTML comment and email extraction, SSL checks and URL fuzzing back-to-back against the URL, printing each module's findings.

### XSS and LFI check on exactly one page

```
vault-scanner -u "http://target.example.com/page.php?id=1" -xss -lfi -this
```

Outcome: tests only the URL given (`-this` disables crawling) for reflected XSS and LFI indicators and prints per-parameter results; drop `-this` to let it crawl the site first.

### Threaded port scan of a host

```
vault-scanner -ip 198.51.100.10 -sp 1 -ep 1024 -t 50
```

Outcome: a 50-thread TCP port scan across 198.51.100.10 ports 1–1024 reporting open ports; sibling switches (`-fin`, `-null`, `-ack`, `-xmas`, `-ping_sweep`, `-arp`, `-os_scan`) select the other probe styles.
