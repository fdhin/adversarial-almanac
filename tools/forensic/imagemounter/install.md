---
tool: "imagemounter"
build-system: "python"
upstream: "https://github.com/ralphje/imagemounter"
recipe: "blackarch"
source: "https://github.com/ralphje/imagemounter"
license: "MIT"
blackarch-pkgver: "413.383b30b"
---

# Installing imagemounter

> Command line utility and Python package to ease the (un)mounting of forensic disk images.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/imagemounter) (version `413.383b30b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S imagemounter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ralphje/imagemounter) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`, `termcolor`
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

Backlinks: [[forensic]] · [[imagemounter]] · [[BlackArch Tools]]

