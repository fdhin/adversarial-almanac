---
tool: "wordpresscan"
build-system: "scripts"
upstream: "https://github.com/swisskyrepo/Wordpresscan"
recipe: "blackarch"
source: "https://github.com/swisskyrepo/Wordpresscan"
license: "custom:unknown"
blackarch-pkgver: "76.f810c1c"
---

# Installing wordpresscan

> WPScan rewritten in Python + some WPSeku ideas.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wordpresscan) (version `76.f810c1c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wordpresscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/swisskyrepo/Wordpresscan.git
cd Wordpresscan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-lxml, python2-requests, python2-tornado (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[wordpresscan]] · [[BlackArch Tools]]

