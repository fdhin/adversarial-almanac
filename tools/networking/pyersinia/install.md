---
tool: "pyersinia"
build-system: "python"
upstream: "https://github.com/nottinghamprisateam/pyersinia"
recipe: "blackarch"
source: "https://github.com/nottinghamprisateam/pyersinia"
license: "MIT"
blackarch-pkgver: "49.73f4056"
---

# Installing pyersinia

> Network attack tool like yersinia but written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyersinia) (version `49.73f4056`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pyersinia    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nottinghamprisateam/pyersinia) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ipy`, `netaddr`, `netifaces`, `pcapy`, `pydnet`, `scapy`, `setuptools`, `sphinx`, `sphinx_rtd_theme`, `termcolor`
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

Backlinks: [[networking]] · [[pyersinia]] · [[BlackArch Tools]]

