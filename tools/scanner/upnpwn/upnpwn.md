---
name: "upnpwn"
gentoo-category: "net-analyzer"
version: "0.8.0-r1"
homepage: "http://bigbrainlabs.blogspot.com/"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: scripts
license: GPL-2
meta-checked: "2026-11-09"
github-repo: "jsharp6968/UPnPwn"
github-stars: 6
github-pushed: "2022-09-13"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# upnpwn

> Interactive UPnP testing client: SSDP discovery, SCPD parsing and manual SOAP interaction.

## What it's for

UPnPwn (Jordan Sharp) is a Python 3 toolkit for manually probing UPnP implementations: it initializes as a UPnP client, discovers devices through SSDP, fetches and parses their SCPD service descriptions, and lets you walk hosts to devices to services to actions through interactive menus, crafting SOAP requests against each action (GENA eventing is present but untested). The stated goal is a foundation for finding new UPnP bugs, with automated exploit launching as future work. It is alpha-quality software (upstream source labels it version 0.5) with no CLI flags — everything happens in the menu after launch — so treat it as structured manual exploration rather than turnkey exploitation.

## Typical use cases

### Discover and explore the local UPnP surface

```bash
python3 upnpwn.py
```

Outcome: the tool initializes as a UPnP client device, multicasts SSDP discovery on the local network, then drops into the interactive menu where discovered hosts, their devices and services, and callable SOAP actions are inspected and invoked step by step.

*No corpus evidence — derived from upstream documentation (jsharp6968/UPnPwn README and upnpwn.py source — menu-driven entry point, no command-line flags).*
