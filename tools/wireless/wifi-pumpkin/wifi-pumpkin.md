---
name: "wifi-pumpkin"
categories:
  - blackarch-wireless
version: "v1.1.7.r2.g344a475"
homepage: "https://github.com/P0cL4bs/wifipumpkin3"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "P0cL4bs/wifipumpkin3"
github-stars: 2521
github-pushed: "2024-01-09"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wifi-pumpkin

> The original P0cL4bs rogue-AP framework (Python 2 era) — deprecated and superseded by [[wifipumpkin3]].

## What it's for

WiFi-Pumpkin was the P0cL4bs framework for rogue access point attacks: stand up a fake AP that forwards the victim's legitimate traffic, deauth clients toward it, sniff probe requests and credentials, run transparent proxies with JavaScript injection, spoof DNS and ARP, and mount captive-portal phishing — all driven from a GUI with a plugin system (dns2proxy, sergio-proxy, captive portals). The original project is dead: its repository was renamed to `P0cL4bs/WiFi-Pumpkin-deprecated` and its README points users at the complete rewrite, wifipumpkin3 (Python 3/PyQt5, Apache-2.0), maintained by mh4x0f. The BlackArch `wifi-pumpkin` package follows that move — it builds and installs wifipumpkin3 — so on a current system the `wifi-pumpkin`/`wifipumpkin3` command you run is the rewrite. Use cases and CLI reference live in the [[wifipumpkin3]] note; this entry is kept only to record the lineage and the package-name mapping.

*No corpus evidence — derived from upstream documentation (deprecated repo README, wifipumpkin3 repo and BlackArch PKGBUILD).*
