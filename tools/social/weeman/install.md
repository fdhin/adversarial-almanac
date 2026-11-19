---
tool: "weeman"
build-system: "scripts"
upstream: "https://github.com/Hypsurus/weeman"
recipe: "blackarch"
source: "https://github.com/Hypsurus/weeman"
license: "GPL3"
blackarch-pkgver: "91.53c2efa"
---

# Installing weeman

> HTTP Server for phishing in python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/weeman) (version `91.53c2efa`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S weeman    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Hypsurus/weeman) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-html5lib, python2-lxml, python2-pdb (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[weeman]] · [[BlackArch Tools]]

