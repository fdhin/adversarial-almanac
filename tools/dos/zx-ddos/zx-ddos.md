---
name: "zx-ddos"
categories:
  - dos
version: "git (2026-09-20)"
homepage: "https://github.com/mrgoofydev/zx-ddos"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "mrgoofydev/zx-ddos"
github-stars: 66
github-pushed: "2026-09-20"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 36
gh-activity-checked: "2026-11-09"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# zx-ddos

> Interactive Python 3 DDoS tester that floods a target with a chosen number of parallel HTTP workers.

## What it's for

ZX-DDoS (MrGoofyDev) is a small educational DDoS tool for controlled
network-stress experiments: given a target URL or IP, it spawns a
configurable number of parallel workers that issue the requested number of
HTTP requests, making the mechanics of volumetric web flooding easy to
observe and measure in a lab. It has no command-line flags — everything is
answered interactively after launch, which keeps it deliberately simple.
It is not a multi-vector framework: no amplification, botnet or slow-loris
modes — for those see [[ufonet]], [[slowloris]] or [[t50]]. Use only
against systems you own or are explicitly authorized to test.

## Typical use cases

### Launch an interactive flood run

Start the tool and answer its three prompts.

```
python3 start.py
```

Outcome: prompts for the target (IP, URL or domain), the number of workers
(parallel threads) and the number of requests; once answered, spawns that
many workers issuing HTTP requests against the target and prints progress
as the run proceeds. Observe the effect on the target's access log and
response latency.

*No corpus evidence — derived from upstream documentation.*
