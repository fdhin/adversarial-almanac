---
tool: "foresight"
build-system: "python"
upstream: "https://github.com/ALSchwalm/foresight"
recipe: "blackarch"
source: "https://github.com/ALSchwalm/foresight"
license: "MIT"
blackarch-pkgver: "0.1.r12.g6f48984"
---

# Installing foresight

> A tool for predicting the output of random number generators.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/foresight) (version `0.1.r12.g6f48984`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S foresight    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ALSchwalm/foresight) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `nose`, `setuptools`
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

Backlinks: [[crypto]] · [[foresight]] · [[BlackArch Tools]]

