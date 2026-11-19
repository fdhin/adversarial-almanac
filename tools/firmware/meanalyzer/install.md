---
tool: "meanalyzer"
build-system: "scripts"
upstream: "https://github.com/platomav/MEAnalyzer"
recipe: "blackarch"
aur-package: "meanalyzer"
source: "https://github.com/platomav/MEAnalyzer"
license: "GPL3"
blackarch-pkgver: "1.273.0"
---

# Installing meanalyzer

> Intel Engine Firmware Analysis Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/meanalyzer) (version `1.273.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S meanalyzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/platomav/MEAnalyzer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `crccheck`, `prettytable`, `ptable-boxchar`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[firmware]] · [[meanalyzer]] · [[BlackArch Tools]]

