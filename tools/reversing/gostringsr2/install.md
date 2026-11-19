---
tool: "gostringsr2"
build-system: "python"
upstream: "https://github.com/CarveSystems/gostringsr2"
recipe: "blackarch"
source: "https://github.com/CarveSystems/gostringsr2"
license: "MIT"
blackarch-pkgver: "1.1.2"
---

# Installing gostringsr2

> Extract strings from a Go binary using radare2.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gostringsr2) (version `1.1.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gostringsr2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CarveSystems/gostringsr2) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `pip`, `r2pipe`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `radare2`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[gostringsr2]] · [[BlackArch Tools]]

