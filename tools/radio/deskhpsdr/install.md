---
tool: "deskhpsdr"
build-system: "cmake"
upstream: "https://github.com/dl1bz/deskhpsdr"
recipe: "blackarch"
source: "https://github.com/dl1bz/deskhpsdr"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2.8.12.r0.g2082188"
---

# Installing deskhpsdr

> SDR App for HPSDR protocol and Soapy-API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/deskhpsdr) (version `2.8.12.r0.g2082188`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S deskhpsdr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/dl1bz/deskhpsdr) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `curl`
  - `fftw`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `i2c-tools`
  - `json-c`
  - `libgpiod`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libpulse`
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pipewire`
  - `pipewire-pulse`
  - `soapysdr`

## Exact build steps from the recipe

```bash
cd $pkgname

  make CFLAGS="${CFLAGS} -D_GNU_SOURCE $(pkg-config --cflags libxml-2.0)" \
    LDLIBS+=" $(pkg-config --libs libxml-2.0)"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[deskhpsdr]] · [[BlackArch Tools]]

