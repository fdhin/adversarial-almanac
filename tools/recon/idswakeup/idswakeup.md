---
name: "idswakeup"
categories:
  - blackarch-recon
version: "1.0"
homepage: "http://www.hsc.fr/ressources/outils/idswakeup/index.html.en"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "SavSanta/idswakeup"
github-stars: 8
github-pushed: "2018-04-21"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# idswakeup

> Vintage NIDS false-positive generator that replays well-known attack signatures at a target to test intrusion detection.

## What it's for

IDSwakeup (Stéphane Aubert, Hervé Schauer Consultants, 2000) is a shell driver plus helper packet crafters (`iwu`, `hping2`) that send traffic mimicking classic attack signatures — CGI probes, back-door traffic, port scans and the like — at a host so a network intrusion detection system can be checked for both detection and false-positive behavior. It is a testing aid from the early NIDS era: it does not exploit anything, and the driver script expects its `iwu` and `hping2` helpers to sit alongside it, so run it from the unpacked source tree.

## Typical use cases

### Fire a battery of attack mimics at a sensor

Point the generator at the host your IDS sensor watches.

```
idswakeup 198.51.100.10 198.51.100.20
```

Outcome: runs the embedded test battery from source address 198.51.100.10 against 198.51.100.20; watch the NIDS console to confirm each crafted signature raises the expected alert — or an unwanted one.

### Control the volume and reach of the generated traffic

Repeat tests with more packets or a constrained TTL.

```
idswakeup 198.51.100.10 198.51.100.20 100 1
```

Outcome: each test in the battery emits 100 packets (`nb`) with the given TTL instead of the single-packet default, letting you shape load and how far the crafted packets travel.

*No corpus evidence — derived from upstream documentation.*
