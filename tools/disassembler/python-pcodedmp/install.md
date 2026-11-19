---
tool: "python-pcodedmp"
build-system: "python"
upstream: "https://pypi.org/project/pcodedmp/#files"
recipe: "split-parent:pcodedmp"
aur-package: "python-pcodedmp"
source: "https://pypi.org/project/pcodedmp/#files"
license: "GPL3"
blackarch-pkgver: "1.2.6"
---

# Installing python-pcodedmp

> A VBA p-code disassembler.

ℹ️ Packaged as a split package — built from the `pcodedmp` recipe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pcodedmp) (version `1.2.6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S python-pcodedmp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/pcodedmp/#files) (the recipe uses a versioned archive).

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

Backlinks: [[disassembler]] · [[python-pcodedmp]] · [[BlackArch Tools]]

