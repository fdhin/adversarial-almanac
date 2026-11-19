---
tool: "sb0x"
build-system: "scripts"
upstream: "https://github.com/lostcitizen/sb0x-project"
recipe: "blackarch"
source: "https://github.com/lostcitizen/sb0x-project"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2.0.1rc3.r2.gd863070"
---

# Installing sb0x

> A simple and Lightweight framework for Penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sb0x) (version `2.0.1rc3.r2.gd863070`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sb0x    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lostcitizen/sb0x-project) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sb0x]] · [[BlackArch Tools]]

