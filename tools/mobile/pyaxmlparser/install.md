---
tool: "pyaxmlparser"
build-system: "python"
upstream: "https://github.com/appknox/pyaxmlparser"
recipe: "blackarch"
source: "https://github.com/appknox/pyaxmlparser"
license: "Apache"
blackarch-pkgver: "v0.3.31.r13.g446bf7c"
---

# Installing pyaxmlparser

> A simple parser to parse Android XML file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyaxmlparser) (version `v0.3.31.r13.g446bf7c`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S pyaxmlparser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/appknox/pyaxmlparser) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `click`, `coverage`, `lxml`, `pytest`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `flake8`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[pyaxmlparser]] · [[BlackArch Tools]]

