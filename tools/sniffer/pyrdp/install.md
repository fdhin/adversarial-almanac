---
tool: "pyrdp"
build-system: "python"
upstream: "https://github.com/GoSecure/pyrdp"
recipe: "blackarch"
source: "https://github.com/GoSecure/pyrdp"
license: "GPL3"
blackarch-pkgver: "2107.b610809"
---

# Installing pyrdp

> Python 3 RDP MITM and library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyrdp) (version `2107.b610809`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pyrdp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GoSecure/pyrdp) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `appdirs`, `cryptography`, `dbus`, `names`, `notify2`, `pyasn1`, `pycryptodome`, `pyopenssl`, `pytz`, `rsa`, `service-identity`, `setuptools`, `twisted`
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

Backlinks: [[sniffer]] · [[pyrdp]] · [[BlackArch Tools]]

