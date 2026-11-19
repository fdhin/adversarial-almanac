---
name: "scrying"
categories:
  - blackarch-webapp
version: "v0.9.2.r0.g9d50ade"
homepage: "https://github.com/nccgroup/scrying"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-03"
build-system: go
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "nccgroup/scrying"
github-stars: 475
github-pushed: "2023-04-03"
github-archived: false
github-issues: 36
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# scrying

> NCC Group tool that captures RDP, VNC and web screenshots of many targets into one browsable HTML report.

## What it's for

scrying grabs screenshots from web pages, RDP and VNC services across a
whole target list and assembles them into a single `output/report.html`
gallery. It exists because EyeWitness dropped RDP support: scrying captures
RDP login screens natively (including VNC with auth), renders web pages with
an installed Chromium, and smartly parses targets from Nmap XML or Nessus
output or a plain targets file — deciding per entry whether it is a web
address, RDP or VNC (explicit `rdp://`/`vnc://` schemes or `--mode` override
the guess). Typical use is the first pass over an external range: see which
services present login portals and what software they run. It does not
authenticate or test anything beyond rendering; headless servers need xvfb,
and web capture requires Chromium installed.

## Typical use cases

### Screenshot a single service

Grab one web page or one RDP/VNC server.

```
scrying -t http://target.example.com
scrying -t rdp://198.51.100.10
scrying -t vnc://198.51.100.10:5901
```

Outcome: captures a PNG into `output/web/`, `output/rdp/` or `output/vnc/`
named after the host and port, and refreshes `output/report.html` with the
image.

### Mass-capture from an nmap XML

Feed port-scan output straight in.

```
nmap -iL targets.txt -p 80,443,8080,8443,3389 -oX targets.xml
scrying --nmap targets.xml
```

Outcome: scrying parses open web and RDP services from the XML and
screenshots each one — the fastest route from a range scan to a visual
inventory, with all results in the HTML report.

### Capture from a mixed targets file

List arbitrary targets and let scrying classify them.

```
scrying -f targets.txt --output-dir /tmp/scrying_outputs
```

Outcome: reads each line (bare IPs, hostnames or `rdp://`/`vnc://`/`http://`
URLs), auto-selects the capture mode, and writes all images plus the report
under the chosen directory. Add `--web-proxy http://127.0.0.1:8080` to route
web capture through a proxy.

*No corpus evidence — derived from upstream documentation (nccgroup/scrying README usage examples and output tree).*
