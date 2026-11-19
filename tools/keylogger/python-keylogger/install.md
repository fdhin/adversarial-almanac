---
tool: "python-keylogger"
build-system: "python"
upstream: "https://github.com/GiacomoLaw/Keylogger"
recipe: "blackarch"
source: "https://github.com/GiacomoLaw/Keylogger"
license: "MIT"
blackarch-pkgver: "2.7.3"
provides: ["keylogger"]
---

# Installing python-keylogger

> Simple keystroke logger.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-keylogger) (version `2.7.3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python-keylogger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GiacomoLaw/Keylogger) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`, `six`, `xlib`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  touch CHANGES.md
  sed 's|xlib||g' -i setup.py # on new release, try to remove

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/keylogger`

Backlinks: [[keylogger]] · [[python-keylogger]] · [[BlackArch Tools]]

