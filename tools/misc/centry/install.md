---
tool: "centry"
build-system: "scripts"
upstream: "https://github.com/0xPoly/Centry"
recipe: "blackarch"
source: "https://github.com/0xPoly/Centry"
license: "GPL3"
blackarch-pkgver: "72.6de2868"
---

# Installing centry

> Cold boot & DMA protection

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/centry) (version `72.6de2868`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S centry    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/0xPoly/Centry.git
cd Centry
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pmw`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[centry]] · [[BlackArch Tools]]

