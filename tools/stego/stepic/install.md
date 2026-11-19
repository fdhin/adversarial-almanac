---
tool: "stepic"
build-system: "scripts"
upstream: "https://domnit.org/stepic/doc/"
recipe: "blackarch"
source: "https://domnit.org/stepic/doc/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.4"
---

# Installing stepic

> A python image steganography tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stepic) (version `0.4`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S stepic    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://domnit.org/stepic/doc/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pillow (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[stego]] · [[stepic]] · [[BlackArch Tools]]

