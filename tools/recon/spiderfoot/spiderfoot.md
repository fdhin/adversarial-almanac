---
name: "spiderfoot"
categories:
  - blackarch-recon
version: "4.0"
homepage: "https://github.com/smicallef/spiderfoot"
packaged-by: [blackarch, kali, aur]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "smicallef/spiderfoot"
github-stars: 24395
github-pushed: "2026-04-13"
github-archived: false
github-issues: 396
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spiderfoot

> OSINT automation framework: 200+ modules feed each other's findings through a correlation engine, driven from a web UI, a full CLI (`sf`) or an interactive client (`sfcli`).

## What it's for

Open-source footprinting of a target entity — IP address, domain or sub-domain, hostname, subnet (CIDR), ASN, e-mail address, phone number, username, person's name or Bitcoin address. Modules run in a publisher/subscriber pipeline where each finding can trigger more lookups (certificate transparency to hostnames to DNS to ports to breach databases), YAML correlation rules (37 ship predefined) flag cross-module indicators, and everything lands in an SQLite back-end exportable as CSV, JSON or GEXF. Most modules need no API key; free or tiered keys unlock SHODAN, SecurityTrails, HaveIBeenPwned, Censys, GreyNoise and dozens more, and locally installed tools (nmap, WhatWeb, DNSTwist, Nuclei, WAFW00F) can be called as modules. Reach for it when one target needs broad, correlated OSINT rather than a single lookup.

## Typical use cases

### Launch the web interface

```
sf -l 127.0.0.1:5001
```

Outcome: starts the embedded web server; browse to `http://127.0.0.1:5001` to configure API keys, start and monitor scans, and browse or export results.

### Scan a target entirely from the command line

```
sf -s target.example.com -u all -o csv > results.csv
```

Outcome: runs a scan selecting modules by use case (`all`, `passive`, `footprint` or `investigate`) and streams collected results to stdout as CSV — no UI involved. Modules can also be selected by name with `-m mod1,mod2`, or by the event types to collect with `-t`; `-q` silences logging.

### Discover modules and event types before a targeted scan

```
sf -M
sf -T
```

Outcome: `-M`/`--modules` lists every available module by name; `-T`/`--types` lists the event types modules can produce — together they are the menu for planning a precise `-m`/`-t` scan.

### Drive a running server interactively

```
sfcli
```

Outcome: opens an interactive shell against a running SpiderFoot server (defaults to `http://127.0.0.1:5001`; use `-s URL -u USER -p PASS` for remote or authenticated servers). Inside the shell, `ping` tests the connection, `scans` lists scans, `data <scanID>` retrieves a scan's results, `export` exports them, `start` launches a new scan and `logs` tails server logs — the scripting surface for the web UI.

*No corpus evidence — derived from upstream documentation and source (smicallef/spiderfoot README; `sf.py` and `sfcli.py` argparse surfaces).*
