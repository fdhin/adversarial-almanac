---
tool: "deblaze"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/deblaze"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/deblaze"
license: "GPL-3.0-or-later"
blackarch-pkgver: "3.a24555d"
---

# Installing deblaze

> Performs method enumeration and interrogation against flash remoting end points.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/deblaze) (version `3.a24555d`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S deblaze    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SpiderLabs/deblaze) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pillow (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[deblaze]] · [[BlackArch Tools]]

