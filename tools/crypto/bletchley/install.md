---
tool: "bletchley"
build-system: "other"
upstream: "https://code.google.com/p/bletchley/"
recipe: "blackarch"
source: "https://code.google.com/p/bletchley/"
license: "GPL"
blackarch-pkgver: "0.0.1"
---

# Installing bletchley

> A collection of practical application cryptanalysis tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bletchley) (version `0.0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bletchley    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/bletchley/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **System libraries** (Arch name — translate for your distro):
  - `python3`
  - `scons`
  - `subversion`

## Exact build steps from the recipe

```bash
cd trunk

  scons
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[bletchley]] · [[BlackArch Tools]]

