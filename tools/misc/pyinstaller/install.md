---
tool: "pyinstaller"
build-system: "python"
upstream: "https://pyinstaller.org"
recipe: "blackarch"
aur-package: "pyinstaller"
source: "https://pyinstaller.org"
license: "Apache-2.0, GPL-2.0-or-later, MIT"
blackarch-pkgver: "6.22.3"
---

# Installing pyinstaller

> Bundles a Python application and all its dependencies into a single package.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyinstaller) (version `6.22.3`, license Apache-2.0, GPL-2.0-or-later, MIT).

## Arch / BlackArch

```bash
sudo pacman -S pyinstaller    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pyinstaller.org) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `altgraph`, `build`, `hatchling`, `installer`, `setuptools`, `wheel`
- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `base-devel`
  - `binutils`
  - `glibc`
  - `pyinstaller-hooks-contrib`
  - `python`
  - `waf`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  # Compile bootloader
  #cd bootloader
  #python ./waf all
  #cd ..

  # Build package
  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[misc]] · [[pyinstaller]] · [[BlackArch Tools]]

