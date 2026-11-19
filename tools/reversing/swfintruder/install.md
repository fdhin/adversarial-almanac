---
tool: "swfintruder"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/swfintruder/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/swfintruder/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.9.1"
---

# Installing swfintruder

> First tool for testing security in Flash movies. A runtime analyzer for SWF external movies. It helps to find flaws in Flash.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/swfintruder) (version `0.9.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S swfintruder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/swfintruder/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[swfintruder]] · [[BlackArch Tools]]

