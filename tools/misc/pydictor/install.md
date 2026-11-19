---
tool: "pydictor"
build-system: "scripts"
upstream: "https://github.com/LandGrey/pydictor"
recipe: "blackarch"
source: "https://github.com/LandGrey/pydictor"
license: "GPL3"
blackarch-pkgver: "100.4a77f0a"
---

# Installing pydictor

> A useful hacker dictionary builder for a brute-force attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pydictor) (version `100.4a77f0a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pydictor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/LandGrey/pydictor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pydictor]] · [[BlackArch Tools]]

