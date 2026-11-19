---
tool: "torpy"
build-system: "python"
upstream: "https://github.com/torpyorg/torpy"
recipe: "blackarch"
source: "https://github.com/torpyorg/torpy"
license: "Apache"
blackarch-pkgver: "60.ebf000c"
---

# Installing torpy

> Pure python Tor client implementation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/torpy) (version `60.ebf000c`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S torpy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/torpyorg/torpy) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `cffi`, `cryptography`, `pycparser`, `requests`, `setuptools`
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

Backlinks: [[networking]] · [[torpy]] · [[BlackArch Tools]]

