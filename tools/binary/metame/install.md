---
tool: "metame"
build-system: "python"
upstream: "https://github.com/a0rtega/metame"
recipe: "blackarch"
source: "https://github.com/a0rtega/metame"
license: "MIT"
blackarch-pkgver: "14.8d583a0"
---

# Installing metame

> A simple metamorphic code engine for arbitrary executables.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/metame) (version `14.8d583a0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S metame    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/a0rtega/metame) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `keystone`, `r2pipe`, `setuptools`, `simplejson`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `radare2`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[metame]] · [[BlackArch Tools]]

