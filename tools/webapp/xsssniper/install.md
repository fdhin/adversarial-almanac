---
tool: "xsssniper"
build-system: "scripts"
upstream: "https://github.com/gbrindisi/xsssniper"
recipe: "blackarch"
source: "https://github.com/gbrindisi/xsssniper"
license: "GPL2"
blackarch-pkgver: "79.02b59af"
---

# Installing xsssniper

> An automatic XSS discovery tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsssniper) (version `79.02b59af`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S xsssniper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gbrindisi/xsssniper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-hgapi, python2-lxml, python2-mechanize (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsssniper]] · [[BlackArch Tools]]

