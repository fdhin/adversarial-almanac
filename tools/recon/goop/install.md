---
tool: "goop"
build-system: "python"
upstream: "https://github.com/s0md3v/goop"
recipe: "blackarch"
source: "https://github.com/s0md3v/goop"
license: "GPL3"
blackarch-pkgver: "12.39b34eb"
---

# Installing goop

> Perform google searches without being blocked by the CAPTCHA or hitting any rate limits.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goop) (version `12.39b34eb`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S goop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/s0md3v/goop) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`
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

Backlinks: [[recon]] · [[goop]] · [[BlackArch Tools]]

