---
tool: "torshammer"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/torshammer/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/torshammer/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing torshammer

> A slow POST Denial of Service testing tool written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/torshammer) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S torshammer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/torshammer/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[torshammer]] · [[BlackArch Tools]]

