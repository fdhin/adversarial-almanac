---
tool: "detectem"
build-system: "python"
upstream: "https://github.com/spectresearch/detectem"
recipe: "blackarch"
source: "https://github.com/spectresearch/detectem"
license: "MIT"
blackarch-pkgver: "276.bc5f073"
provides: ["det"]
---

# Installing detectem

> Detect software and its version on websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/detectem) (version `276.bc5f073`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S detectem    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/spectresearch/detectem) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `click-log`, `docker`, `dukpy`, `environs`, `parsel`, `pytest`, `requests`, `setuptools`, `yaml`, `zope-interface`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/det`

Backlinks: [[fingerprint]] · [[detectem]] · [[BlackArch Tools]]

