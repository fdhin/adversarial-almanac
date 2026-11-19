---
tool: "yaaf"
build-system: "scripts"
upstream: "https://github.com/RomeuG/YAAF"
recipe: "blackarch"
source: "https://github.com/RomeuG/YAAF"
license: "GPL-1.0-or-later"
blackarch-pkgver: "7.4d6273a"
---

# Installing yaaf

> Yet Another Admin Finder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yaaf) (version `7.4d6273a`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S yaaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/RomeuG/YAAF.git
cd YAAF
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[yaaf]] · [[BlackArch Tools]]

