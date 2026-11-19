---
tool: "featherduster"
build-system: "scripts"
upstream: "https://github.com/nccgroup/featherduster"
recipe: "blackarch"
source: "https://github.com/nccgroup/featherduster"
license: "custom:unknown"
blackarch-pkgver: "191.9229158"
---

# Installing featherduster

> An automated, modular cryptanalysis tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/featherduster) (version `191.9229158`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S featherduster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/featherduster) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ncurses`
  - `python2`
  - `readline`
- ⚠️ Python 2 only deps: python2-gmpy, python2-ishell, python2-pycryptodome, python2-requests, python2-setuptools, python2-singlefile (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[featherduster]] · [[BlackArch Tools]]

