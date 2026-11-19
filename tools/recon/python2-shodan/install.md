---
tool: "python2-shodan"
build-system: "python"
upstream: "https://pypi.org/project/shodan/#files"
recipe: "blackarch"
source: "https://pypi.org/project/shodan/#files"
license: "MIT"
blackarch-pkgver: "1.28.0"
---

# Installing python2-shodan

> Python library and command-line utility for Shodan (https://developer.shodan.io).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python2-shodan) (version `1.28.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python2-shodan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/shodan/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-click, python2-click-plugins, python2-colorama, python2-requests, python2-setuptools, python2-simplejson, python2-xlsxwriter (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[python2-shodan]] · [[BlackArch Tools]]

