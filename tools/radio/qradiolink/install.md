---
tool: "qradiolink"
build-system: "make"
upstream: "https://github.com/qradiolink/qradiolink"
recipe: "blackarch"
nix-package: "qradiolink"
aur-package: "qradiolink"
source: "https://github.com/qradiolink/qradiolink"
license: "GPL-3.0-or-later, LGPL-3.0-or-later, MIT"
blackarch-pkgver: "0.9.2.2.r0.g6a6966f"
---

# Installing qradiolink

> Multimode SDR transceiver for GNU radio, ADALM-Pluto, LimeSDR, USRP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/qradiolink) (version `0.9.2.2.r0.g6a6966f`, license GPL-3.0-or-later, LGPL-3.0-or-later, MIT).

## Arch / BlackArch

```bash
sudo pacman -S qradiolink    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/qradiolink/qradiolink) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `alsa-lib` — Debian libasound2-dev / Fedora alsa-lib-devel
  - `boost`
  - `boost-libs`
  - `codec2`
  - `cppzmq`
  - `gnuradio`
  - `gnuradio-osmosdr`
  - `gstreamer`
  - `libconfig`
  - `libftdi`
  - `libjpeg-turbo`
  - `libsndfile`
  - `libuhd`
  - `libvolk`
  - `limesuite`
  - `log4cpp`
  - `opus`
  - `pipewire-pulse`
  - `protobuf`
  - `qt5-base`
  - `qt5-multimedia`
  - `soapysdr`
  - `speexdsp`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd src/ext/
  protoc --cpp_out=. Mumble.proto
  protoc --cpp_out=. QRadioLink.proto
  cd ../../build/
  qmake \
    CONFIG+=link_pkgconfig \
    PKGCONFIG+=protobuf \
    INCLUDEPATH+=/usr/include/libftdi1 \
    ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[radio]] · [[qradiolink]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#qradiolink    # ad-hoc run, no install
nix profile install nixpkgs#qradiolink   # or persist it
```
