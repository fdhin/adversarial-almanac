---
tool: "valhalla-api"
build-system: "python"
upstream: "https://github.com/NextronSystems/valhallaAPI"
recipe: "blackarch"
source: "https://github.com/NextronSystems/valhallaAPI"
license: "Apache"
blackarch-pkgver: "87.c010a48"
---

# Installing valhalla-api

> Valhalla API Client.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/valhalla-api) (version `87.c010a48`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S valhalla-api    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NextronSystems/valhallaAPI) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `packaging`, `pytest`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[valhalla-api]] · [[BlackArch Tools]]

