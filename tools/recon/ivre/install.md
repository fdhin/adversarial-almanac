---
tool: "ivre"
build-system: "python"
upstream: "https://ivre.rocks/"
recipe: "blackarch"
aur-package: "ivre"
source: "https://ivre.rocks/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.0.0"
---

# Installing ivre

> Network recon framework based on Nmap, Masscan, Zeek (Bro), Argus, Netflow,...

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ivre) (version `0.0.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ivre    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://ivre.rocks/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgbase

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/ivre`

Backlinks: [[recon]] · [[ivre]] · [[BlackArch Tools]]

