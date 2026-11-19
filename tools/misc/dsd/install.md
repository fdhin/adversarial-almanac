---
tool: "dsd"
build-system: "cmake"
upstream: "https://github.com/szechyjs/dsd"
recipe: "blackarch"
aur-package: "dsd"
source: "https://github.com/szechyjs/dsd"
license: "GPL-2.0-only"
blackarch-pkgver: "v1.6.0.r96.g59423fa"
---

# Installing dsd

> Digital Speech Decoder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsd) (version `v1.6.0.r96.g59423fa`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S dsd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/szechyjs/dsd) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `itpp`
  - `libsndfile`
  - `mbelib`
  - `portaudio` — Debian libportaudio2 / Fedora portaudio-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake ../ \
    -DCMAKE_POLICY_VERSION_MINIMUM=3.5 \
    -DCMAKE_INSTALL_PREFIX=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[dsd]] · [[BlackArch Tools]]

