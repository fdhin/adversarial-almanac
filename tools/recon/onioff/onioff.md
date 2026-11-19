---
name: "onioff"
categories:
  - blackarch-recon
version: "84.34dc309"
homepage: "https://github.com/k4m4/onioff"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "k4m4/onioff"
github-stars: 717
github-pushed: "2022-08-04"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# onioff

> Onion-service URL inspector that reports which .onion links are alive and what each one serves.

## What it's for

ONIOFF (k4m4 / Nikolaos Kamarinakis) is a small Python 3 script for inspecting Tor hidden-service ("onion") URLs before you visit them. Given one onion address or a file of them, it connects through Tor and reports the service's status — whether it is online, the page title, and related details — so you can triage a list of deep-web links without opening them in a browser. It requires a correctly configured, running Tor instance on the box (it talks to the local SOCKS port). It only fetches and summarizes the landing page; it is not a crawler or an onion-content scanner.

## Typical use cases

### Inspect a single onion service

Check one .onion URL directly.

```
python3 onioff.py http://facebookcorewwwi.onion/
```

Outcome: ONIOFF resolves the service over Tor and prints its inspection verdict (online/offline plus page details); on a healthy service you get the title and status of the hidden service.

### Batch-inspect a list and save only active onions

Feed a file of onion URLs and write a report, keeping only services that respond.

```
python3 onioff.py -f ~/onions.txt -o ~/report.txt -a
```

Outcome: every URL in `onions.txt` is inspected in turn; `report.txt` receives the results, and with `-a` only onions found active are logged to the output file.

> No corpus evidence — derived from upstream documentation.
