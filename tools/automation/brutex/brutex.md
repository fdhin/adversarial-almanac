---
name: "brutex"
categories:
  - blackarch-automation
version: "114.eacd084"
homepage: "https://github.com/1N3/BruteX"
packaged-by: [blackarch]
blackarch-builddate: "2023-05-21"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "1N3/BruteX"
github-stars: 2320
github-pushed: "2024-08-18"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# brutex

> One-shot wrapper that brute-forces usernames and passwords on a single target host and port.

## What it's for

BruteX is a thin automation wrapper from the Sn1per tool family: given a target and a port, it automatically brute-forces "all services running on a target" — checking open ports, usernames and passwords behind the scenes. The interface is deliberately minimal (`brutex target <port>`); upstream no longer documents which underlying tools it drives, and the project is effectively dormant (last push 2024). Use it for a quick unattended single-target check; for anything you need to control — protocols, wordlists, threading, reporting — dedicated tools such as hydra, medusa or brutespray are better documented.

## Typical use cases

### Brute-force a service on a specific port

Point the wrapped workflow at one host and port.

```
brutex 198.51.100.10 22
```

Outcome: runs the enumeration and credential brute-force chain against the host and port; progress and any recovered credentials are printed to the terminal as each phase completes.

*No corpus evidence — derived from upstream documentation.*
