---
tool: "phantomcollect"
build-system: "python"
upstream: "https://github.com/xsser01/phantomcollect"
recipe: "blackarch"
aur-package: "phantomcollect"
source: "https://github.com/xsser01/phantomcollect"
license: "MIT"
blackarch-pkgver: "1.0.0"
---

# Installing phantomcollect

> Lightweight stealth web data collection framework for ethical security testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phantomcollect) (version `1.0.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S phantomcollect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/xsser01/phantomcollect) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `flask`, `installer`, `pysqlite3`, `requests`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[phantomcollect]] · [[BlackArch Tools]]

