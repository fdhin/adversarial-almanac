---
tool: "citadel"
build-system: "scripts"
upstream: "https://github.com/jakecreps/Citadel"
recipe: "blackarch"
aur-package: "citadel"
source: "https://github.com/jakecreps/Citadel"
license: "custom:unknown"
blackarch-pkgver: "95.3b1adbc"
---

# Installing citadel

> A library of OSINT tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/citadel) (version `95.3b1adbc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S citadel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jakecreps/Citadel.git
cd Citadel
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[citadel]] · [[BlackArch Tools]]

