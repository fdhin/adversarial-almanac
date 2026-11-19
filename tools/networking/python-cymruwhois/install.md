---
tool: "python-cymruwhois"
build-system: "python"
upstream: "https://pypi.org/project/cymruwhois/#files"
recipe: "blackarch"
aur-package: "python-cymruwhois"
source: "https://pypi.org/project/cymruwhois/#files"
license: "unknown"
blackarch-pkgver: "1.6"
---

# Installing python-cymruwhois

> Python client for the whois.cymru.com service

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-cymruwhois) (version `1.6`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S python-cymruwhois    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/cymruwhois/#files) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[python-cymruwhois]] · [[BlackArch Tools]]

