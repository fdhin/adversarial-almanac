---
tool: "dripcap"
build-system: "scripts"
upstream: "https://github.com/dripcap/dripcap"
recipe: "blackarch"
source: "https://github.com/dripcap/dripcap"
license: "MIT"
blackarch-pkgver: "0.6.15"
---

# Installing dripcap

> Caffeinated Packet Analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dripcap) (version `0.6.15`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dripcap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dripcap/dripcap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dripcap]] · [[BlackArch Tools]]

