---
tool: "buster"
build-system: "python"
upstream: "https://github.com/sham00n/buster"
recipe: "blackarch"
source: "https://github.com/sham00n/buster"
license: "GPL-3.0-or-later"
blackarch-pkgver: "92.131437e"
---

# Installing buster

> Find emails of a person and return info associated with them.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/buster) (version `92.131437e`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S buster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sham00n/buster) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `certifi`, `gevent`, `grequests`, `lxml`, `pip`, `requests`, `setuptools`, `wheel`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cython`
  - `python`
  - `twint`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[buster]] · [[BlackArch Tools]]

