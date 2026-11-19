---
tool: "fbht"
build-system: "scripts"
upstream: "https://github.com/chinoogawa/fbht"
recipe: "blackarch"
source: "https://github.com/chinoogawa/fbht"
license: "custom"
blackarch-pkgver: "70.d75ae93"
---

# Installing fbht

> A Facebook Hacking Tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fbht) (version `70.d75ae93`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S fbht    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/chinoogawa/fbht) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-matplotlib, python2-mechanize, python2-networkx, python2-numpy, python2-selenium, python2-setuptools, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[fbht]] · [[BlackArch Tools]]

