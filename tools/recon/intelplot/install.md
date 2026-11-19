---
tool: "intelplot"
build-system: "scripts"
upstream: "https://github.com/itsmehacker/IntelPlot"
recipe: "blackarch"
source: "https://github.com/itsmehacker/IntelPlot"
license: "MIT"
blackarch-pkgver: "12.4dd9fc0"
---

# Installing intelplot

> OSINT Tool to Mark Points on Offline Map.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/intelplot) (version `12.4dd9fc0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S intelplot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/itsmehacker/IntelPlot.git
cd IntelPlot
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[intelplot]] · [[BlackArch Tools]]

