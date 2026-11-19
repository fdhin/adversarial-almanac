---
name: "camscan"
categories:
  - blackarch-scanner
version: "1.0057215"
homepage: "https://github.com/securestate/camscan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "securestate/camscan"
github-stars: 9
github-pushed: "2015-02-11"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# camscan

> Audits the CAM (MAC address) tables of Cisco switches over SSH and reports anomalies.

## What it's for

Camscan (SecureState, v0.1) logs into each Cisco switch on your list over SSH, pulls its CAM table, and flags anomalies — MAC addresses showing up where they should not, such as unknown devices on trunk or uplink ports — by comparing against a list of known-good MAC addresses you supply. Findings go to a CSV report. It audits only the switches you feed it and classifies only the MACs you pre-approve; it neither discovers switches nor enforces anything. Note the flag quirk: `-h` here is `--hosts` (the switch list file), not help.

## Typical use cases

### Audit a switch list

```
camscan -h switches.txt -m known_macs.txt
```

Outcome: SSHes into every switch listed in switches.txt (credentials prompted per switch unless defaults are given), pulls each CAM table, and writes anomalies to the default report.csv; `-r report2.csv` chooses a different output file.

### Run with default credentials and more logging

```
camscan -h switches.txt -m known_macs.txt -u netadmin -p Password1 -L INFO
```

Outcome: authenticates everywhere as netadmin instead of prompting, and `-L` (DEBUG/INFO/WARNING/ERROR/CRITICAL) raises the logging level so each switch's processing is visible.

*No corpus evidence — derived from the packaged source (argparse interface).*
