---
tool: "wpbf"
build-system: "scripts"
upstream: "https://github.com/dejanlevaja/wpbf"
recipe: "blackarch"
source: "https://github.com/dejanlevaja/wpbf"
license: "GPL2"
blackarch-pkgver: "7.11b6ac1"
---

# Installing wpbf

> Multithreaded WordPress brute forcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpbf) (version `7.11b6ac1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wpbf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dejanlevaja/wpbf) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[wpbf]] · [[BlackArch Tools]]

