---
tool: "xxxpwn-smart"
build-system: "scripts"
upstream: "https://github.com/aayla-secura/xxxpwn_smart"
recipe: "blackarch"
source: "https://github.com/aayla-secura/xxxpwn_smart"
license: "GPL3"
blackarch-pkgver: "6.b11b95b"
---

# Installing xxxpwn-smart

> A fork of xxxpwn adding further optimizations and tweaks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xxxpwn-smart) (version `6.b11b95b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xxxpwn-smart    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aayla-secura/xxxpwn_smart) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xxxpwn-smart]] · [[BlackArch Tools]]

