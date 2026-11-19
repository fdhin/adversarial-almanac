---
tool: "binwally"
build-system: "scripts"
upstream: "https://github.com/bmaia/binwally"
recipe: "blackarch"
source: "https://github.com/bmaia/binwally"
license: "MIT"
blackarch-pkgver: "4.0aabd8b"
---

# Installing binwally

> Binary and Directory tree comparison tool using the Fuzzy Hashing concept (ssdeep).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/binwally) (version `4.0aabd8b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S binwally    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bmaia/binwally) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cython2`
  - `python2`
  - `ssdeep`
- ⚠️ Python 2 only deps: python2-pydeep (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[binwally]] · [[BlackArch Tools]]

