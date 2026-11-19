---
name: "dga-detection"
categories:
  - blackarch-recon
version: "78.0a3186e"
homepage: "https://github.com/philarkwright/DGA-Detection"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "philarkwright/DGA-Detection"
github-stars: 55
github-pushed: "2017-09-09"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dga-detection

> Bigram-frequency classifier that watches DNS traffic and flags algorithmically generated domains.

## What it's for

This tool detects DGA (domain generation algorithm) callbacks — the random-
looking domains malware uses to reach its C2 — by scoring how "human" a
domain looks. It trains a bigram frequency baseline from known DGA domain
lists (a Tinba list ships in `data/dga_training.txt`), whitelists against the
Alexa top 1M, and then either batch-tests domains or sniffs live DNS queries
(scapy) on an interface, alerting when a queried domain is built almost
entirely of low-frequency bigrams. Upstream reports 100% detection on 100,000
GameoverZeus domains at an 8% false-positive rate before whitelisting. It is
a Python 2 research script from 2017 and still explicitly
work-in-progress: expect tuning (the settings-file baseline threshold trades
detection rate for false positives) and noisy alerts on some CDN/browser
prefetch traffic. Run it on a DNS-serving box you control.

## Typical use cases

### Train the detection model

Build the bigram baseline once before any detection works.

```
sudo dga-detection
```

Outcome: opens the interactive menu; choose `1. Train Data` to compute the
bigram frequency model from `data/dga_training.txt` and write the baseline to
the settings file under `data/`. Until this exists, both capture and testing
modes refuse to run with a "run the training algorithm first" message.

### Watch an interface for DGA lookups

Monitor live DNS traffic headlessly.

```
sudo dga-detection -o 2 -i eth0
```

Outcome: loads the Alexa whitelist and trained baseline, then sniffs port 53
traffic on eth0; every queried second-level domain whose bigram score crosses
the baseline prints a "Warning! Potential DGA Detected" block with the domain
and the src -> dst IPs of the query (an optional Pushbullet key can push the
same alert). Stop with Ctrl-C; the `-o 2` form skips the menu entirely, which
is what makes nohup/cron operation possible.

No corpus evidence — derived from upstream documentation and the packaged
`dga_detection.py` argument parser.
