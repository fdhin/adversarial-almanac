---
tool: "simple-lan-scan3"
build-system: "scripts"
upstream: "https://codeberg.org/jahway603/Simple-LAN-Scanner3"
recipe: "blackarch"
source: "https://codeberg.org/jahway603/Simple-LAN-Scanner3"
license: "custom:unknown"
blackarch-pkgver: "1.0.1"
---

# Installing simple-lan-scan3

> A simple python3 script that leverages scapy for discovering live hosts on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simple-lan-scan3) (version `1.0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S simple-lan-scan3    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://codeberg.org/jahway603/Simple-LAN-Scanner3) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `scapy`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[simple-lan-scan3]] · [[BlackArch Tools]]

