---
tool: "sipi"
build-system: "scripts"
upstream: "https://github.com/ST2Labs/SIPI"
recipe: "blackarch"
source: "https://github.com/ST2Labs/SIPI"
license: "GPL2"
blackarch-pkgver: "13.58f0dcc"
---

# Installing sipi

> Simple IP Information Tools for Reputation Data Analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sipi) (version `13.58f0dcc`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sipi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ST2Labs/SIPI.git
cd SIPI
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[sipi]] · [[BlackArch Tools]]

