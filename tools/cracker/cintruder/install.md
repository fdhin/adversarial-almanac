---
tool: "cintruder"
build-system: "scripts"
upstream: "https://github.com/epsylon/cintruder"
recipe: "blackarch"
source: "https://github.com/epsylon/cintruder"
license: "GPL-3.0-or-later"
blackarch-pkgver: "14.f8a3f12"
---

# Installing cintruder

> An automatic pentesting tool to bypass captchas.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cintruder) (version `14.f8a3f12`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S cintruder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/epsylon/cintruder) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml, python2-pillow, python2-pycurl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cintruder]] · [[BlackArch Tools]]

