---
tool: "drupwn"
build-system: "python"
upstream: "https://github.com/immunIT/drupwn"
recipe: "blackarch"
source: "https://github.com/immunIT/drupwn"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.0.4.r2.g8186732"
---

# Installing drupwn

> Drupal enumeration & exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/drupwn) (version `1.0.4.r2.g8186732`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S drupwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/immunIT/drupwn) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `prompt_toolkit`, `pysocks`, `requests`, `setuptools`, `veryprettytable`
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

Backlinks: [[webapp]] · [[drupwn]] · [[BlackArch Tools]]

