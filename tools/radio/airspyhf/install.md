---
tool: "airspyhf"
build-system: "cmake"
upstream: "https://airspy.com"
recipe: "blackarch"
nix-package: "airspyhf"
source: "https://airspy.com"
license: "BSD"
blackarch-pkgver: "1.6.8.r75.g24fe8ff"
---

# Installing airspyhf

> Host code for AirspyHF+ SDR.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/airspyhf) (version `1.6.8.r75.g24fe8ff`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S airspyhf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://airspy.com) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libusb` — Debian libusb-1.0-0-dev / Fedora libusbx-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build
  cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr -Wno-dev ../
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[airspyhf]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#airspyhf    # ad-hoc run, no install
nix profile install nixpkgs#airspyhf   # or persist it
```
