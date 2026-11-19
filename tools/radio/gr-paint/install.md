---
tool: "gr-paint"
build-system: "cmake"
upstream: "https://github.com/drmpeg/gr-paint"
recipe: "blackarch"
source: "https://github.com/drmpeg/gr-paint"
license: "GPL-3.0-or-later"
blackarch-pkgver: "53.c443e36"
provides: ["tgatoluma"]
---

# Installing gr-paint

> An OFDM Spectrum Painter for GNU Radio.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gr-paint) (version `53.c443e36`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S gr-paint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/drmpeg/gr-paint) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `doxygen`
  - `gnuradio`
  - `gnuradio-companion`
  - `pybind11`
- ⚠️ Python 2 only deps: python2-pillow (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname/build"

  make

  cd "$srcdir/$pkgname"

  gcc -o tgatoluma tgatoluma.c
```

## Installed layout (per recipe)

- Commands: `/usr/bin/tgatoluma`

Backlinks: [[radio]] · [[gr-paint]] · [[BlackArch Tools]]

