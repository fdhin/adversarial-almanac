---
tool: "pyinstaller-hooks-contrib"
build-system: "python"
upstream: "https://github.com/pyinstaller/pyinstaller-hooks-contrib"
recipe: "blackarch"
aur-package: "pyinstaller-hooks-contrib"
source: "https://github.com/pyinstaller/pyinstaller-hooks-contrib"
license: "custom:mixed"
blackarch-pkgver: "2026.9"
---

# Installing pyinstaller-hooks-contrib

> PyInstaller community hooks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyinstaller-hooks-contrib) (version `2026.9`, license custom:mixed).

## Arch / BlackArch

```bash
sudo pacman -S pyinstaller-hooks-contrib    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pyinstaller/pyinstaller-hooks-contrib) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `installer`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pyinstaller-hooks-contrib]] · [[BlackArch Tools]]

