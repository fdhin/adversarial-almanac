---
tool: "bandicoot"
build-system: "python"
upstream: "https://pypi.org/project/bandicoot/#files"
recipe: "blackarch"
source: "https://pypi.org/project/bandicoot/#files"
license: "MIT"
blackarch-pkgver: "0.6.0"
---

# Installing bandicoot

> A toolbox to analyze mobile phone metadata.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bandicoot) (version `0.6.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S bandicoot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/bandicoot/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[bandicoot]] · [[BlackArch Tools]]

