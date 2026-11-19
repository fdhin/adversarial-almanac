---
tool: "wayparam"
build-system: "python"
upstream: "https://github.com/aleff-github/wayparam"
recipe: "blackarch"
source: "https://github.com/aleff-github/wayparam"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.0.0"
---

# Installing wayparam

> Fetch and normalize parameterized URLs from the Wayback CDX API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wayparam) (version `1.0.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S wayparam    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aleff-github/wayparam) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `httpx`, `pip`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wayparam]] · [[BlackArch Tools]]

