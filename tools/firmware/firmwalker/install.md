---
tool: "firmwalker"
build-system: "scripts"
upstream: "https://github.com/craigz28/firmwalker"
recipe: "blackarch"
source: "https://github.com/craigz28/firmwalker"
license: "GPL3"
blackarch-pkgver: "101.23ff299"
---

# Installing firmwalker

> Script for searching the extracted firmware file system for goodies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firmwalker) (version `101.23ff299`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S firmwalker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/craigz28/firmwalker) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[firmware]] · [[firmwalker]] · [[BlackArch Tools]]

