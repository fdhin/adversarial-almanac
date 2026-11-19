---
tool: "pyrasite"
build-system: "python"
upstream: "https://pypi.org/project/pyrasite/#files"
recipe: "blackarch"
source: "https://pypi.org/project/pyrasite/#files"
license: "GPL3"
blackarch-pkgver: "2.0"
---

# Installing pyrasite

> Code injection and introspection of running Python processes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyrasite) (version `2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pyrasite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/pyrasite/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gdb`
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python2 ./setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[pyrasite]] · [[BlackArch Tools]]

