---
tool: "swamp"
build-system: "scripts"
upstream: "https://github.com/jakecreps/swamp"
recipe: "blackarch"
aur-package: "swamp"
source: "https://github.com/jakecreps/swamp"
license: "custom:unknown"
blackarch-pkgver: "59.3c8be65"
---

# Installing swamp

> An OSINT tool for discovering associated sites through Google Analytics Tracking IDs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/swamp) (version `59.3c8be65`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S swamp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jakecreps/swamp) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[swamp]] · [[BlackArch Tools]]

