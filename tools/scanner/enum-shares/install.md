---
tool: "enum-shares"
build-system: "scripts"
upstream: "https://github.com/dejanlevaja/enum_shares"
recipe: "blackarch"
source: "https://github.com/dejanlevaja/enum_shares"
license: "GPL2"
blackarch-pkgver: "7.97cba5a"
---

# Installing enum-shares

> Tool that enumerates shared folders across the network and under a custom user account.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enum-shares) (version `7.97cba5a`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S enum-shares    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/dejanlevaja/enum_shares.git
cd enum_shares
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-ipcalc, python2-pysmb, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[enum-shares]] · [[BlackArch Tools]]

