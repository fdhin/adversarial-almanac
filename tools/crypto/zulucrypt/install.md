---
tool: "zulucrypt"
build-system: "cmake"
upstream: "https://github.com/mhogomchungu/zuluCrypt"
recipe: "blackarch"
aur-package: "zulucrypt"
source: "https://github.com/mhogomchungu/zuluCrypt"
license: "GPL-2.0-or-later"
blackarch-pkgver: "7.1.1"
---

# Installing zulucrypt

> Front end to cryptsetup and tcplay and it allows easy management of encrypted block devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zulucrypt) (version `7.1.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S zulucrypt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/mhogomchungu/zuluCrypt) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `cryptsetup`
  - `libpwquality`
  - `libsecret`
  - `libxkbcommon-x11`
  - `qt5-base`
  - `util-linux`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  mkdir -p build
  cd build
  cmake \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DLIB_SUFFIX=lib \
    -DNOGUI=false \
    -DQT5=true \
    -DHOMEMOUNTPREFIX=false \
    -DCMAKE_BUILD_TYPE=release \
    -DWITH_UUID=e2fs . ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[zulucrypt]] · [[BlackArch Tools]]

