---
tool: "mobiusft"
build-system: "autotools"
upstream: "http://savannah.nongnu.org/projects/mobiusft"
recipe: "blackarch"
source: "http://savannah.nongnu.org/projects/mobiusft"
license: "GPL2"
blackarch-pkgver: "1.12"
provides: ["mobius", "mobius_bin.py", "mobius_bin.sh"]
---

# Installing mobiusft

> An open-source forensic framework written in Python/GTK that manages cases and case items, providing an abstract interface for developing extensions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mobiusft) (version `1.12`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mobiusft    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://savannah.nongnu.org/projects/mobiusft) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `pygtk`
  - `python2`
  - `sleuthkit`
- ⚠️ Python 2 only deps: python2-cairo, python2-lxml (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  PYTHON=python2 ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/mobius`, `/usr/bin/mobius_bin.py`, `/usr/bin/mobius_bin.sh`

Backlinks: [[forensic]] · [[mobiusft]] · [[BlackArch Tools]]

