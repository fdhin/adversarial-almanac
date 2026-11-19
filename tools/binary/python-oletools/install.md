---
tool: "python-oletools"
build-system: "python"
upstream: "https://pypi.org/project/oletools/"
recipe: "split-parent:oletools"
aur-package: "python-oletools"
source: "https://pypi.org/project/oletools/"
license: "GPL2"
blackarch-pkgver: "0.60.2"
---

# Installing python-oletools

> Tools to analyze Microsoft OLE2 files.

ℹ️ Packaged as a split package — built from the `oletools` recipe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/oletools) (version `0.60.2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S python-oletools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/oletools/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgbase-$pkgver-2"

  python2 setup.py build

  cd "$srcdir/$pkgbase-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[python-oletools]] · [[BlackArch Tools]]

