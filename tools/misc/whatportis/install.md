---
tool: "whatportis"
build-system: "python"
upstream: "https://github.com/ncrocfer/whatportis"
recipe: "blackarch"
source: "https://github.com/ncrocfer/whatportis.git"
license: "MIT"
blackarch-pkgver: "54.59a1718"
---

# Installing whatportis

> A command to search port names and numbers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/whatportis) (version `54.59a1718`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S whatportis    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ncrocfer/whatportis.git
cd whatportis
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `flask`, `prettytable`, `requests`, `setuptools`, `simplejson`, `tinydb`
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

Backlinks: [[misc]] · [[whatportis]] · [[BlackArch Tools]]

