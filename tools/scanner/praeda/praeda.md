---
name: "praeda"
categories:
  - blackarch-scanner
version: "49.b0b17d3"
homepage: "https://github.com/percx/Praeda"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "percx/Praeda"
github-stars: 194
github-pushed: "2024-08-03"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# praeda

> Praeda — a Perl harvester that sweeps embedded devices (printers and similar network gear) from nmap output or target lists, pulling configuration pages and credential data into per-project logs.

## What it's for

Praeda (0.02.3.x, percx) automates data harvesting from embedded devices: given nmap results or a list of targets, it visits each web interface on the specified port, recognizes known product families, and runs per-product job modules (`jobs/MA*.pl`) to extract configuration, status and credential information. Output goes into a named project folder of logs and exported data, which is the point — repeatable, archived sweeps of device fleets. It expects you to have scoped the network first (nmap or a target file); it enumerates devices rather than being a port scanner itself.

## Typical use cases

### Harvest from nmap gnmap results

Feed an nmap grepable-output file directly.

```
perl praeda.pl -g scan.gnmap -j acmewidget -l results
```

Outcome: Praeda reads the gnmap file, picks out web-serviced hosts, runs its product jobs against them, and writes everything under an `acmewidget` project folder plus the `results` log file.

### Sweep a CIDR or target list on one port

Point it straight at a range or a newline-delimited file of targets.

```
perl praeda.pl -t 198.51.100.0/24 -p 80 -j site-audit -l site-audit.log
```

Outcome: every address in the CIDR (or listed in the file passed to `-t`) is probed on TCP port 80 — one port per run is the documented limit — with recognized devices enumerated by their job module and output collected under the `site-audit` project folder.

*No corpus evidence — derived from upstream documentation (README.txt in percx/Praeda; the option list and both syntax forms are quoted there verbatim).*
