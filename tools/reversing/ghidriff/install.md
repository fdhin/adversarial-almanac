---
tool: "ghidriff"
build-system: "python"
upstream: "https://github.com/clearbluejar/ghidriff"
recipe: "blackarch"
source: "https://github.com/clearbluejar/ghidriff"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v1.0.0.r6.g4f5d895"
---

# Installing ghidriff

> Python Command-Line Ghidra Binary Diffing Engine.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghidriff) (version `v1.0.0.r6.g4f5d895`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ghidriff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/clearbluejar/ghidriff) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `mdutils`, `pyghidra`, `setuptools`
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

Backlinks: [[reversing]] · [[ghidriff]] · [[BlackArch Tools]]

