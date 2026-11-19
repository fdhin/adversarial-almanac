---
tool: "gr-gsm"
build-system: "cmake"
upstream: "https://github.com/ptrkrysik/gr-gsm"
recipe: "blackarch"
source: "https://github.com/ptrkrysik/gr-gsm"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1191.71e9630"
---

# Installing gr-gsm

> Gnuradio blocks and tools for receiving GSM transmissions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gr-gsm) (version `1191.71e9630`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S gr-gsm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/ptrkrysik/gr-gsm) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Python (pip)**: `docutils`, `pyqt5`, `scipy`, `yaml`
- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `cppunit`
  - `doxygen`
  - `gcc-libs`
  - `gnuradio`
  - `gnuradio-companion`
  - `gnuradio-osmosdr`
  - `graphviz`
  - `libosmocore`
  - `libosmosdr`
  - `lksctp-tools`
  - `log4cpp`
  - `pybind11`
  - `pygtk`
  - `python`
  - `qwt`
  - `swig`
  - `talloc`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build
  cmake -DCMAKE_INSTALL_PREFIX=/usr ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[gr-gsm]] · [[BlackArch Tools]]

