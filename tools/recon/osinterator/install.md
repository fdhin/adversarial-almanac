---
tool: "osinterator"
build-system: "scripts"
upstream: "https://github.com/guitarmanj/OSINTerator"
recipe: "blackarch"
source: "https://github.com/guitarmanj/OSINTerator"
license: "MIT"
blackarch-pkgver: "3.8447f58"
---

# Installing osinterator

> Open Source Toolkit for Open Source Intelligence Gathering.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/osinterator) (version `3.8447f58`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S osinterator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/guitarmanj/OSINTerator.git
cd OSINTerator
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-xgoogle (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[osinterator]] · [[BlackArch Tools]]

