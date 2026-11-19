---
name: "iptodomain"
categories:
  - blackarch-recon
version: "18.f1afcd7"
homepage: "https://github.com/Hackplayers/iptodomain"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Hackplayers/iptodomain"
github-stars: 23
github-pushed: "2017-01-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# iptodomain

> Reverse lookup that enumerates domains hosted on an IP range using VirusTotal's passive DNS history.

## What it's for

iptodomain (Hackplayers, Juan Esteban Valencia Pantoja) walks an IP range and asks VirusTotal's API which domains have historically resolved to each address — a passive way to enumerate subdomains and virtual hosts for bug bounty or infrastructure mapping, starting from an organization's known netblocks (often found via its AS number). A VirusTotal API key must be pasted into the script source before use. It is a Python 2-era tool and performs no active scanning of the targets.

## Typical use cases

### Extract domains for an IP range

Sweep a netblock and collect every known domain.

```
iptodomain -i 198.51.100.1 -f 198.51.100.254 -o domains.txt -r report.txt -v
```

Outcome: for each address in the range, VirusTotal's historic resolution data is queried; found domains accumulate in `domains.txt` (`-o`), a deduplicated report is written to `report.txt` (`-r`), and `-v` prints per-IP progress while scanning.

### Collect a combined report across scans

Merge results from multiple ranges into one artifact.

```
iptodomain -i 198.51.100.1 -f 198.51.100.254 -w all-domains.txt
```

Outcome: appends every domain found to the `-w` report file alongside per-address detail, keeping a running combined dataset across successive range sweeps.

*No corpus evidence — derived from upstream documentation.*
