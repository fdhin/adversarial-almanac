---
tool: "python2-utidylib"
build-system: "python"
upstream: "http://utidylib.berlios.de"
recipe: "blackarch"
source: "http://utidylib.berlios.de"
license: "MIT"
blackarch-pkgver: "0.6"
---

# Installing python2-utidylib

> Python bindings for Tidy HTML parser/cleaner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python2-utidylib) (version `0.6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python2-utidylib    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://utidylib.berlios.de) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `tidyhtml`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[python2-utidylib]] · [[BlackArch Tools]]

