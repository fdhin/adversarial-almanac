---
tool: "slither"
build-system: "python"
upstream: "https://github.com/crytic/slither"
recipe: "blackarch"
aur-package: "slither"
source: "https://github.com/crytic/slither"
license: "AGPL3"
blackarch-pkgver: "5205.97c1d10d8"
---

# Installing slither

> Solidity static analysis framework written in Python 3.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/slither) (version `5205.97c1d10d8`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S slither    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/crytic/slither) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `crytic-compile`, `prettytable`, `pysha3`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[slither]] · [[BlackArch Tools]]

