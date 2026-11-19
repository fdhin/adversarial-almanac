---
tool: "regipy"
build-system: "python"
upstream: "https://github.com/mkorman90/regipy"
recipe: "blackarch"
source: "https://github.com/mkorman90/regipy"
license: "MIT"
blackarch-pkgver: "2.2.2"
---

# Installing regipy

> Library for parsing offline registry hives.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/regipy) (version `2.2.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S regipy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mkorman90/regipy) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `attrs`, `click`, `construct`, `inflection`, `jsonlines`, `logbook`, `pytz`, `setuptools`, `tabulate`, `tqdm`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[regipy]] · [[BlackArch Tools]]

