---
tool: "spipscan"
build-system: "scripts"
upstream: "https://github.com/PaulSec/SPIPScan"
recipe: "blackarch"
source: "https://github.com/PaulSec/SPIPScan"
license: "MIT"
blackarch-pkgver: "69.4ad3235"
---

# Installing spipscan

> SPIP (CMS) scanner for penetration testing purpose written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spipscan) (version `69.4ad3235`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S spipscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PaulSec/SPIPScan.git
cd SPIPScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[spipscan]] · [[BlackArch Tools]]

