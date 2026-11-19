---
tool: "sdrangel"
build-system: "cmake"
upstream: "https://github.com/f4exb/sdrangel"
recipe: "blackarch"
nix-package: "sdrangel"
source: "https://github.com/DTolm/VkFFT.git"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v8.0.0.r102.g4a9f7bc"
---

# Installing sdrangel

> Qt6/OpenGL SDR and signal analyzer frontend.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sdrangel) (version `v8.0.0.r102.g4a9f7bc`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sdrangel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/DTolm/VkFFT.git
cd VkFFT
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `airspy`
  - `airspyhf`
  - `aptdec`
  - `bladerf`
  - `boost`
  - `cm256cc`
  - `codec2`
  - `doxygen`
  - `dsdcc`
  - `faad2`
  - `ffmpeg`
  - `fftw`
  - `glslang`
  - `graphviz`
  - `hackrf`
  - `hamlib`
  - `libdab`
  - `libiio`
  - `libmirisdr4`
  - `libperseus-sdr`
  - `libsdrplay`
  - `libuhd`
  - `libxtrx`
  - `limesuite`
  - `lz4`
  - `nanomsg`
  - `ninja`
  - `opencv`
  - `pulse-native-provider`
  - `qt6-base`
  - `qt6-charts`
  - `qt6-declarative`
  - `qt6-location`
  - `qt6-multimedia`
  - `qt6-scxml`
  - `qt6-serialport`
  - `qt6-speech`
  - `qt6-svg`
  - `qt6-tools`
  - `qt6-webengine`
  - `qt6-websockets`
  - `rtl-sdr`
  - `sgp4`
  - `spirv-headers`
  - `spirv-tools`
  - `vulkan-headers`
  - `vulkan-icd-loader`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
# https://bugs.gentoo.org/704322
  # also enable x86-64-v3 (SSE3+) optimizations for DSP-heavy code
  export CFLAGS="$CFLAGS -march=x86-64-v3"
  export CXXFLAGS="$CXXFLAGS -fpermissive -march=x86-64-v3"

  local cmake_options=(
    -Wno-dev
    -G Ninja
    -DARCH_OPT=""
    -DCMAKE_BUILD_TYPE=None
    -DCMAKE_INSTALL_PREFIX=/usr
    -DLIBDSDCC_INCLUDE_DIR=/usr/include/dsdcc
    -DCM256CC_INCLUDE_DIR=/usr/include/cm256cc
    # Build against Qt6
    -DENABLE_QT6=ON
    # Disable DAB demod plugin because it currently FTBFS
    # https://github.com/f4exb/sdrangel/issues/2520
    -DENABLE_CHANNELRX_DEMODDAB=OFF
    # Tell SDRangel to use VkFFT headers we vendor'ed in $srcdir/VkFFT
    -DVKFFT_INCLUDE_DIR="${srcdir}/VkFFT/vkFFT"
    # Select backend = Vulkan (0), CUDA (1). Select Vulkan to cover more devices
    -DVKFFT_BACKEND=0
  )

  cmake -S "$pkgname" -B build "${cmake_options[@]}"
  ninja -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[sdrangel]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sdrangel    # ad-hoc run, no install
nix profile install nixpkgs#sdrangel   # or persist it
```
