---
tool: "xssless"
build-system: "scripts"
upstream: "https://github.com/mandatoryprogrammer/xssless"
recipe: "blackarch"
source: "https://github.com/mandatoryprogrammer/xssless.git"
license: "GPL2"
blackarch-pkgver: "45.8e7ebe1"
---

# Installing xssless

> An automated XSS payload generator written in python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xssless) (version `45.8e7ebe1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S xssless    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mandatoryprogrammer/xssless.git
cd xssless
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xssless]] · [[BlackArch Tools]]

