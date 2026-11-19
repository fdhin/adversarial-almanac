---
tool: "mitm"
build-system: "scripts"
upstream: "https://github.com/Th3Hurrican3/mitm"
recipe: "blackarch"
source: "https://github.com/Th3Hurrican3/mitm"
license: "custom:unknown"
blackarch-pkgver: "8.bd2b351"
---

# Installing mitm

> A simple yet effective python3 script to perform DNS spoofing via ARP poisoning.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mitm) (version `8.bd2b351`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mitm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Th3Hurrican3/mitm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `netifaces`, `python-nmap`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[mitm]] · [[BlackArch Tools]]

