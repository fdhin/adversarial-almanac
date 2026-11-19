---
tool: "sniffles"
build-system: "python"
upstream: "https://github.com/petabi/sniffles"
recipe: "blackarch"
aur-package: "sniffles"
source: "https://github.com/petabi/sniffles"
license: "GPL3"
blackarch-pkgver: "469.118e93f"
provides: ["rulegen"]
---

# Installing sniffles

> A Packet Capture Generator for IDS and Regular Expression Evaluation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sniffles) (version `469.118e93f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sniffles    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/petabi/sniffles) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`, `sortedcontainers`, `wheel`
- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/rulegen`

Backlinks: [[networking]] · [[sniffles]] · [[BlackArch Tools]]

