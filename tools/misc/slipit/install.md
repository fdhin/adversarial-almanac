---
tool: "slipit"
build-system: "python"
upstream: "https://github.com/usdAG/slipit"
recipe: "blackarch"
source: "https://github.com/usdAG/slipit"
license: "GPL-3.0-only"
blackarch-pkgver: "1.0.1"
---

# Installing slipit

> Utility for creating archives with path traversal elements for ZipSlip attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/slipit) (version `1.0.1`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S slipit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/usdAG/slipit) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `magic`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[slipit]] · [[BlackArch Tools]]

