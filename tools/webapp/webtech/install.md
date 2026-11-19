---
tool: "webtech"
build-system: "python"
upstream: "https://pypi.org/project/webtech/#files"
recipe: "blackarch"
source: "https://pypi.org/project/webtech/#files"
license: "GPL"
blackarch-pkgver: "1.3.3"
---

# Installing webtech

> Identify technologies used on websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webtech) (version `1.3.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S webtech    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/webtech/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[webtech]] · [[BlackArch Tools]]

