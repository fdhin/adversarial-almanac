---
tool: "powerstager"
build-system: "python"
upstream: "https://github.com/z0noxz/powerstager"
recipe: "blackarch"
source: "https://github.com/z0noxz/powerstager"
license: "custom:unknown"
blackarch-pkgver: "14.0149dc9"
---

# Installing powerstager

> A payload stager using PowerShell.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powerstager) (version `14.0149dc9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S powerstager    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/z0noxz/powerstager) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `names`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mingw-w64-gcc`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[powerstager]] · [[BlackArch Tools]]

