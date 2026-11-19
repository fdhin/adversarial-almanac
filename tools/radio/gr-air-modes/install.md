---
tool: "gr-air-modes"
build-system: "cmake"
upstream: "https://github.com/bistromath/gr-air-modes"
recipe: "blackarch"
source: "https://github.com/bistromath/gr-air-modes"
license: "GPL3"
blackarch-pkgver: "396.0b6c383"
---

# Installing gr-air-modes

> Gnuradio tools for receiving Mode S transponder signals, including ADS-B.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gr-air-modes) (version `396.0b6c383`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gr-air-modes    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/bistromath/gr-air-modes) (the recipe uses a versioned archive).

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
  - `gnuradio-osmosdr`
  - `libuhd`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `swig`
- ⚠️ Python 2 only deps: python2-numpy, python2-pyqt4, python2-pyzmq, python2-scipy (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgname/build"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[gr-air-modes]] · [[BlackArch Tools]]

