---
name: "opendoor"
categories:
  - blackarch-webapp
version: "5.13.2"
homepage: "https://github.com/stanislav-web/OpenDoor"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-05-04"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "stanislav-web/OpenDoor"
github-stars: 1013
github-pushed: "2026-11-05"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 154
gh-activity-checked: "2026-11-09"
---

# opendoor

> OWASP-oriented web directory scanner with subdomain scans, response sniffers, WAF handling, and multi-format reporting.

## What it's for

OpenDoor discovers content on web targets: directories and files, subdomains, and interesting responses (directory listings, exposed secrets, stack traces, open redirects). Scans are deeply tunable — request method, status and size filtering, auto-calibration against soft-404 responses, retries and delays — and results land in std/JSON/CSV/HTML/SQLite/SARIF reports. It fingerprints the target's technology stack, detects WAFs, paces itself in WAF-safe mode, and accepts single hosts, host lists mixing URLs, domains, IPv4 addresses, CIDR blocks and ranges, or stdin. An actively maintained Python 3 project, also published on PyPI.

## Typical use cases

### Scan a single host for directories

```
opendoor --host https://example.com
```

Outcome: enumerates directory and file paths against the host and reports discovered locations; add `--crawl` to also extract and report same-origin links from already-scanned responses.

### Enumerate subdomains

```
opendoor --host example.com --scan subdomains
```

Outcome: switches the scan to subdomain discovery for the domain and reports the subdomains found.

### Scan a list of mixed targets

```
opendoor --hostlist targets.txt
```

Outcome: reads targets.txt, which may mix URLs, bare domains, IPv4 addresses, CIDR blocks, and inclusive IP ranges, and runs the scan against each (`cat targets.txt | opendoor --stdin` accepts the same format on standard input).

### Fingerprint the target's stack

```
opendoor --host https://example.com --fingerprint
```

Outcome: identifies the web technology in front of the host (server, framework, CMS) before deeper scanning, so wordlists and checks can be tailored.

### Low-noise scan with response sniffers

```
opendoor --host https://example.com --method GET --auto-calibrate --include-status 200-299,301,302 --sniff endpoint,secret,indexof,stacktrace --reports std,json,csv
```

Outcome: filters responses by status with calibration for soft-404s, classifies interesting bodies (exposed endpoints, secrets, directory listings, stack traces), and writes findings to the terminal plus JSON and CSV files.

### Pace a scan past a WAF

```
opendoor --host https://example.com --waf-safe-mode --timeout 60 --retries 5 --delay 0.5
```

Outcome: runs the same discovery with conservative timing and retry behavior suited to targets behind a web application firewall; `--proxy socks5://127.0.0.1:9050` instead routes the traffic through a proxy.

No corpus evidence — derived from upstream documentation.
