---
tool: "loic"
build-system: "scripts"
upstream: "https://github.com/NewEraCracker/LOIC"
recipe: "blackarch"
aur-package: "loic"
source: "https://github.com/NewEraCracker/LOIC"
license: "GPL"
blackarch-pkgver: "2.9.9.99"
---

# Installing loic

> An open source network stress tool for Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/loic) (version `2.9.9.99`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S loic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NewEraCracker/LOIC) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `mono`
- **System libraries** (Arch name — translate for your distro):
  - `monodevelop`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/pixmaps`, `/usr/share/windows`

Backlinks: [[networking]] · [[loic]] · [[BlackArch Tools]]

