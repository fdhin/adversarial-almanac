---
tool: "reconnoitre"
build-system: "python"
upstream: "https://github.com/codingo/Reconnoitre"
recipe: "blackarch"
source: "https://github.com/codingo/Reconnoitre"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.r338.gf62afba"
---

# Installing reconnoitre

> A security tool for multithreaded information gathering and service enumeration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/reconnoitre) (version `1.r338.gf62afba`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S reconnoitre    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/codingo/Reconnoitre.git
cd Reconnoitre
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nbtscan`
  - `net-snmp`
  - `nmap`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[reconnoitre]] · [[BlackArch Tools]]

