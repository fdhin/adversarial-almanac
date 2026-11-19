---
tool: "dsd-fme"
build-system: "cmake"
upstream: "https://github.com/lwvmobile/dsd-fme"
recipe: "blackarch"
aur-package: "dsd-fme"
source: "https://github.com/lwvmobile/dsd-fme"
license: "custom:copyright"
blackarch-pkgver: "2024"
---

# Installing dsd-fme

> Digital Speech Decoder - Florida Man Edition.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsd-fme) (version `2024`, license custom:copyright).

## Arch / BlackArch

```bash
sudo pacman -S dsd-fme    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/lwvmobile/dsd-fme) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `blas`
  - `codec2`
  - `dbus`
  - `fftw`
  - `flac`
  - `gcc-libs`
  - `glibc`
  - `itpp`
  - `lame`
  - `lapack`
  - `libasyncns`
  - `libcap`
  - `libgpg-error`
  - `libogg`
  - `libpulse`
  - `libsndfile`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `libvorbis`
  - `libxau`
  - `libxcb`
  - `libxdmcp`
  - `lz4`
  - `mbelib`
  - `mpg123`
  - `ncurses`
  - `opus`
  - `rtl-sdr`
  - `systemd-libs`
  - `xz`
  - `zstd`

## Exact build steps from the recipe

```bash
cmake -B build -S "$srcdir/$pkgname-$pkgver" \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -Wno-dev

  make -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[dsd-fme]] · [[BlackArch Tools]]

