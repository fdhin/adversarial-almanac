---
tool: "shadowfinder"
build-system: "python"
upstream: "https://github.com/bellingcat/ShadowFinder"
recipe: "blackarch"
source: "https://github.com/bellingcat/ShadowFinder"
license: "MIT"
blackarch-pkgver: "0.7.1.r0.g215485a"
---

# Installing shadowfinder

> Find possible locations of shadows around the world.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shadowfinder) (version `0.7.1.r0.g215485a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shadowfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bellingcat/ShadowFinder) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `basemap`, `build`, `fire`, `matplotlib`, `numpy`, `pandas`, `pip`, `pytz`, `suncalc`, `timezonefinder`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[shadowfinder]] · [[BlackArch Tools]]

