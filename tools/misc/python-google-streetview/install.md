---
tool: "python-google-streetview"
build-system: "python"
upstream: "https://pypi.org/project/google-streetview/#files"
recipe: "split-parent:google-streetview"
source: "https://pypi.org/project/google-streetview/#files"
license: "MIT"
blackarch-pkgver: "1.2.9"
---

# Installing python-google-streetview

> A command line tool and module for Google Street View Image API.

ℹ️ Packaged as a split package — built from the `google-streetview` recipe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/google-streetview) (version `1.2.9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python-google-streetview    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/google-streetview/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver-2"

  python2 setup.py build

  cd "$srcdir/$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[python-google-streetview]] · [[BlackArch Tools]]

