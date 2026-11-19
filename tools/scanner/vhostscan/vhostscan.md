---
name: "vhostscan"
categories:
  - blackarch-scanner
version: "1.21.r68.gacc7890"
homepage: "https://github.com/codingo/VHostScan"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "codingo/VHostScan"
github-stars: 1312
github-pushed: "2025-08-18"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# vhostscan

> Virtual-host discovery by Host-header brute force — with catch-all detection, fuzzy content matching, and options built for scanning through pivots.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Enumerating the hostnames served behind one web IP. vhostscan sends candidate hostnames (from its bundled wordlists or from stdin) in Host headers against a target and diffs the responses, using content-length analysis and fuzzy similarity between unique replies to separate real virtual hosts from catch-all configurations and dynamic default pages. It fits pivot workflows: point it at a localhost SSH forward while formatting headers for the real host and port (`-b`, `-p`, `-r`), and it can send simple WAF-bypass headers or rotate random user agents. It enumerates vhosts; it does not scan whatever it finds. Upstream's binary is `VHostScan`; the BlackArch package installs it as `vhostscan`.

## Typical use cases

### Default enumeration of a domain

```
vhostscan -t example.com
```

Outcome: walks the default virtual-host wordlist against example.com on port 80 and prints the vhosts found, with unique-response analysis weeding out catch-all and default pages.

### Enumerate through an SSH port-forward

```
vhostscan -t localhost -b example.com -p 4444 -r 80
```

Outcome: probes localhost:4444 (the tunnel) but sends Host headers for example.com on port 80 — the pivot-safe combination when the real webserver is only reachable through the forward.

### Drive candidates from stdin

```
cat candidate-hosts.txt | vhostscan -t 198.51.100.10
```

Outcome: stdin is consumed as the wordlist, so externally generated hostname candidates (DNS enumeration output, permutations) are scanned directly; a `-w` wordlist given as well is appended to the stdin data.

### HTTPS scan with a bundled wordlist

```
vhostscan -t example.com -w ./wordlists/cloud-modern.txt --ssl
```

Outcome: scans over HTTPS with the cloud/infrastructure-oriented wordlist instead of the general-purpose default — the shape to reach for when hunting modern DevOps-style subdomains.
