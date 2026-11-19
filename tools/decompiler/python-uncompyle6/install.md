---
tool: "python-uncompyle6"
build-system: "python"
upstream: "https://pypi.org/project/uncompyle6/#files"
recipe: "blackarch"
aur-package: "python-uncompyle6"
source: "https://pypi.org/project/uncompyle6/#files"
license: "GPL-3.0-or-later"
blackarch-pkgver: "3.9.3"
---

# Installing python-uncompyle6

> A Python cross-version decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-uncompyle6) (version `3.9.3`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S python-uncompyle6    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/uncompyle6/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `hypothesis`, `installer`, `pytest`, `setuptools`, `spark-parser`, `wheel`, `xdis`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[python-uncompyle6]] · [[BlackArch Tools]]

