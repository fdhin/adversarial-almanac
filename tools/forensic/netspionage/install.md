---
tool: "netspionage"
build-system: "scripts"
upstream: "https://github.com/ANG13T/netspionage"
recipe: "blackarch"
source: "https://github.com/ANG13T/netspionage"
license: "custom:unknown"
blackarch-pkgver: "99.c24f995"
---

# Installing netspionage

> Network Forensics CLI utility that performs Network Scanning, OSINT, and Attack Detection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netspionage) (version `99.c24f995`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S netspionage    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ANG13T/netspionage) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pandas`, `pick`, `requests`, `scapy`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[netspionage]] · [[BlackArch Tools]]

