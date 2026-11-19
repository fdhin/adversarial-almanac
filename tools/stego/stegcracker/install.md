---
tool: "stegcracker"
build-system: "python"
upstream: "https://github.com/Paradoxis/StegCracker/tags"
recipe: "blackarch"
source: "https://github.com/Paradoxis/StegCracker/tags"
license: "MIT"
blackarch-pkgver: "2.1.0"
---

# Installing stegcracker

> Steganography brute-force utility to uncover hidden data inside files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stegcracker) (version `2.1.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S stegcracker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Paradoxis/StegCracker/tags) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[stegcracker]] · [[BlackArch Tools]]

