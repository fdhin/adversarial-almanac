---
name: "bluefog"
categories:
  - blackarch-bluetooth
version: "0.0.4"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2019-12-24"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "MS3FGX/Bluefog"
github-stars: 31
github-pushed: "2013-04-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-09"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bluefog

> Generates an essentially unlimited stream of phantom Bluetooth devices by repeatedly re-identifying one or more adapters.

*No corpus evidence — derived from upstream documentation.*

## What it's for

bluefog (Tom Nardi / MS3FGX) stress-tests Bluetooth monitoring and scanning deployments: it cycles an adapter's BD_ADDR, device name and class so that every inquiry from a scanner looks like a brand-new device. That is useful for checking how a sensor network, wardriving rig or discoverable-device logger (like [[bluelog]], by the same author) behaves under device floods, and for generating realistic device traffic. It drives up to six adapters in parallel (one phantom source per radio works far better than one radio alone), can hold a fixed custom name, and can slow the spoof rate ("loiter mode") to mimic slow-moving targets. The catch: it rewrites adapter identity at the HCI level, and some chipsets never recover their real MAC afterwards — use a disposable dongle. Tested with CSR and Broadcom based adapters.

## Typical use cases

### Flood a scanner with phantom devices from one adapter

```
sudo bluefog -d 20 -v
```

Outcome: continuously randomizes the first adapter's MAC and name every ~20 seconds (`-d`, minimum 5, default 30; below ~20 is unreliable), printing each newly spoofed identity in verbose mode. Any nearby inquiry scanner now reports a stream of different devices; stop with Ctrl+C and reboot the adapter to restore normal behavior.

### Simulate realistic multi-device traffic on several adapters

```
sudo bluefog -t 4 -n "Site Survey" -l
```

Outcome: runs four parallel spoofing threads — one per attached radio — with every phantom device carrying the fixed name `Site Survey` instead of a random one, while loiter mode (`-l`) randomly spaces out the spoofs to imitate slow-moving foot traffic rather than a constant flood, closer to what real Bluetooth monitoring sees.
