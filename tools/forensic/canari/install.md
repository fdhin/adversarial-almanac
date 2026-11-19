---
tool: "canari"
build-system: "python"
upstream: "https://pypi.org/project/canari/#files"
recipe: "blackarch"
source: "https://pypi.org/project/canari/#files"
license: "GPL2"
blackarch-pkgver: "3.3.10"
---

# Installing canari

> Maltego rapid transform development and execution framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/canari) (version `3.3.10`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S canari    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/canari/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-boto3, python2-click, python2-colorama, python2-flask, python2-future, python2-lxml, python2-mr.bob, python2-pip, python2-pyasn1, python2-pyopenssl, python2-safedexml, python2-service-identity, python2-setuptools, python2-six, python2-stringcase, python2-twistedx (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  #pip2 install pyasn1 # temporal workaround to avoid setuptool issues

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[canari]] · [[BlackArch Tools]]

