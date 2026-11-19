---
tool: "knxmap"
build-system: "python"
upstream: "https://github.com/ernw/knxmap"
recipe: "blackarch"
source: "https://github.com/ernw/knxmap"
license: "GPL3"
blackarch-pkgver: "252.6f40dd1"
provides: ["knxmap", "knxmap.py"]
---

# Installing knxmap

> KNXnet/IP scanning and auditing tool for KNX home automation installations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/knxmap) (version `252.6f40dd1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S knxmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ernw/knxmap) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  #python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/knxmap`, `/usr/bin/knxmap.py`

Backlinks: [[scanner]] · [[knxmap]] · [[BlackArch Tools]]

