---
tool: "waybackpack"
build-system: "python"
upstream: "https://github.com/jsvine/waybackpack"
recipe: "blackarch"
aur-package: "waybackpack"
source: "https://github.com/jsvine/waybackpack.git"
license: "MIT"
blackarch-pkgver: "v0.6.4.r1.gc5507a0"
---

# Installing waybackpack

> Download the entire Wayback Machine archive for a given URL.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/waybackpack) (version `v0.6.4.r1.gc5507a0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S waybackpack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jsvine/waybackpack.git
cd waybackpack
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

Backlinks: [[webapp]] · [[waybackpack]] · [[BlackArch Tools]]

