---
tool: "exrex"
build-system: "python"
upstream: "https://github.com/asciimoo/exrex"
recipe: "blackarch"
source: "https://github.com/asciimoo/exrex"
license: "AGPL3"
blackarch-pkgver: "153.f180f21"
---

# Installing exrex

> Irregular methods on regular expressions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exrex) (version `153.f180f21`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S exrex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/asciimoo/exrex) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`, `wheel`
- **Build tools**: `git`
- ⚠️ Python 2 only deps: python2-pip, python2-setuptools, python2-wheel (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$_pkgname-2"

  python2 setup.py build

  cd "$srcdir/$_pkgname"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[exrex]] · [[BlackArch Tools]]

