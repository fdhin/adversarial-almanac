---
name: "lbmap"
categories:
  - blackarch-fingerprint
version: "147.2d15ace"
homepage: "https://github.com/wireghoul/lbmap"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL3
meta-checked: "2026-11-09"
github-repo: "wireghoul/lbmap"
github-stars: 45
github-pushed: "2017-03-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# lbmap

> Detect load balancers, reverse proxies and WAFs in front of a web server, plus which modules an Apache has loaded.

## What it's for

lbmap packages two proof-of-concept Perl fingerprinting tools by Eldar
"Wireghoul" Marcussen, presented at OWASP AppSec Asia 2012. `lbmap` probes a
URL to determine what intermediaries — load balancers, reverse proxies, web
application firewalls and other web agents — sit in front of the actual web
server, where they are otherwise invisible to a normal request. `aprof`
remotely detects which modules an Apache server has loaded by exploiting
differences in how loaded and absent modules handle requests. Both are
alpha/PoC quality: the current output is the debug dump of the fingerprinting
process rather than a polished report, and `--version`, `--timeout` (lbmap
also `--batch`, aprof also `--force`) are the documented options.

## Typical use cases

### Map what fronts a web service

```
lbmap http://target.example.com/
```

Outcome: sends individual characterization requests and dumps the
fingerprinting process, exposing load balancer / reverse proxy / WAF behavior
detected in front of the origin server. Add `--batch` to queue the target
non-interactively.

### Detect loaded Apache modules

```
aprof http://target.example.com/
```

Outcome: probes module-specific request handling and reports which Apache
modules are loaded on the remote server — useful to gauge the attack surface
(e.g. mod_rewrite, mod_security) before further testing.

*No corpus evidence — derived from upstream documentation.*
