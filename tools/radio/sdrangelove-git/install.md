---
tool: "sdrangelove-git"
ebuild-package: "sdrangelove-git"
build-system: "cmake"
upstream: "http://sdr.osmocom.org/trac/wiki/sdrangelove"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sdrangelove-git"
version: "20180313.r175"
license: "GPL3"
---

# Installing sdrangelove-git

> A project that allows you to control all the process inside a GNSS receiver, from the raw bits at the output of an analog-to-digital converter to the computation of the navigation solution, that is, obtaining receiver's position and time

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sdrangelove-git) (version `20180313.r175`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S sdrangelove-git
```

The same PKGBUILD is usually on the AUR (`paru -S sdrangelove-git`).

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone git://git.osmocom.org/sdrangelove.git
cd sdrangelove
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `desktop-file-utils`
  - `gnuradio-osmosdr`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `qt5-multimedia`

## Exact build steps from the recipe

```bash
cd "${pkgname}" || exit
  mkdir build && cd build
  cmake -DCMAKE_INSTALL_PREFIX=/usr -DPYTHON_EXECUTABLE=/usr/bin/python2 -DCMAKE_SKIP_RPATH=TRUE ../
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[radio]] · [[sdrangelove-git]] · [[ArchStrike Gap Analysis]]
