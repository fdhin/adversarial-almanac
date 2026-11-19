---
tool: "gitem"
build-system: "python"
upstream: "https://github.com/mschwager/gitem"
recipe: "blackarch"
source: "https://github.com/mschwager/gitem"
license: "GPL3"
blackarch-pkgver: "104.d40a1c9"
---

# Installing gitem

> A Github organization reconnaissance tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gitem) (version `104.d40a1c9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gitem    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mschwager/gitem) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gitem]] · [[BlackArch Tools]]

