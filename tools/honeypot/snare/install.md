---
tool: "snare"
build-system: "python"
upstream: "https://github.com/mushorg/snare"
recipe: "blackarch"
source: "https://github.com/mushorg/snare"
license: "GPL3"
blackarch-pkgver: "188.b17fdfe"
---

# Installing snare

> Super Next generation Advanced Reactive honeypot.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snare) (version `188.b17fdfe`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S snare    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mushorg/snare) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `aiohttp-jinja2`, `beautifulsoup4`, `cssutils`, `gitpython`, `pycodestyle`, `setuptools`
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

Backlinks: [[honeypot]] · [[snare]] · [[BlackArch Tools]]

