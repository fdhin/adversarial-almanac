---
tool: "dabstick-radio"
ebuild-package: "dabstick-radio"
build-system: "cmake"
upstream: "http://www.sdr-j.tk/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/dabstick-radio"
version: "0.96"
license: "GPL2"
---

# Installing dabstick-radio

> The DAB decoding software (SDR-J)

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/dabstick-radio) (version `0.96`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S dabstick-radio
```

The same PKGBUILD is usually on the AUR (`paru -S dabstick-radio`).

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.sdr-j.tk/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `faad2`
  - `fftw`
  - `portaudio` — Debian libportaudio2 / Fedora portaudio-devel
  - `qwt`
  - `rtl-sdr`

## Exact build steps from the recipe

```bash
cd $srcdir/dabstick-radio-${pkgver}/dabreceiver-V2
  mkdir build && cd build
  export CFLAGS="${CFLAGS} -Wno-unused"
  export CXXFLAGS="${CFLAGS}"
  cmake ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[radio]] · [[dabstick-radio]] · [[ArchStrike Gap Analysis]]
