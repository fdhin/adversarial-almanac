---
tool: "goodork"
build-system: "scripts"
upstream: "http://goo-dork.blogspot.com/"
recipe: "blackarch"
source: "http://goo-dork.blogspot.com/"
license: "custom"
blackarch-pkgver: "2.2"
---

# Installing goodork

> A python script designed to allow you to leverage the power of google dorking straight from the comfort of your command line.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goodork) (version `2.2`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S goodork    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/k3170makan/GooDork.git
cd GooDork
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[goodork]] · [[BlackArch Tools]]

