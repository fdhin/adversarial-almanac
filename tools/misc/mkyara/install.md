---
tool: "mkyara"
build-system: "python"
upstream: "https://github.com/fox-it/mkYARA"
recipe: "blackarch"
source: "https://github.com/fox-it/mkYARA"
license: "GPL-3.0-or-later"
blackarch-pkgver: "3.8147f91"
---

# Installing mkyara

> Tool to generate YARA rules based on binary code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mkyara) (version `3.8147f91`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S mkyara    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fox-it/mkYARA) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `setuptools`, `yara-python-dex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[mkyara]] · [[BlackArch Tools]]

