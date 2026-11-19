---
tool: "laf"
build-system: "scripts"
upstream: "https://github.com/takeshixx/laf"
recipe: "blackarch"
source: "https://github.com/takeshixx/laf"
license: "custom:unknown"
blackarch-pkgver: "12.7a456b3"
---

# Installing laf

> Login Area Finder: scans host/s for login panels.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/laf) (version `12.7a456b3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S laf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/takeshixx/laf) (the recipe uses a versioned archive).

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

Backlinks: [[scanner]] · [[laf]] · [[BlackArch Tools]]

