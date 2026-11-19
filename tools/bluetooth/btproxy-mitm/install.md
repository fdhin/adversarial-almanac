---
tool: "btproxy-mitm"
build-system: "other"
upstream: "https://github.com/conorpp/btproxy"
recipe: "blackarch"
source: "https://github.com/conorpp/btproxy"
license: "GPL3"
blackarch-pkgver: "71.cd1c906"
provides: ["btproxy"]
---

# Installing btproxy-mitm

> Man in the Middle analysis tool for Bluetooth.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/btproxy-mitm) (version `71.cd1c906`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S btproxy-mitm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/conorpp/btproxy.git
cd btproxy
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `python2`
- ⚠️ Python 2 only deps: python2-dbus, python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd $pkgname

  python2 setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/btproxy`

Backlinks: [[bluetooth]] · [[btproxy-mitm]] · [[BlackArch Tools]]

