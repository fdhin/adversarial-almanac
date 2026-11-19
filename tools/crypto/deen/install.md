---
tool: "deen"
build-system: "python"
upstream: "https://github.com/takeshixx/deen"
recipe: "blackarch"
source: "https://github.com/takeshixx/deen"
license: "Apache"
blackarch-pkgver: "601.fd9aebe"
---

# Installing deen

> Generic data encoding/decoding application built with PyQt5.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/deen) (version `601.fd9aebe`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S deen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/takeshixx/deen) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `bcrypt`, `dicttoxml`, `jose`, `jsbeautifier`, `lxml`, `pyopenssl`, `pyqt5`, `setuptools`, `xmltodict`
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

Backlinks: [[crypto]] · [[deen]] · [[BlackArch Tools]]

