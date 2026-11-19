---
tool: "apkstudio"
build-system: "cmake"
upstream: "http://www.vaibhavpandey.com/apkstudio/"
recipe: "blackarch"
source: "http://www.vaibhavpandey.com/apkstudio/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "6.3.0.r2.g8fdd381"
---

# Installing apkstudio

> An IDE for decompiling/editing & then recompiling of android application binaries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apkstudio) (version `6.3.0.r2.g8fdd381`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S apkstudio    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.vaibhavpandey.com/apkstudio/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `android-apktool`
  - `android-tools`
  - `qt5-tools`
  - `qt6-base`
  - `unzip`
  - `vulkan-headers`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake -B build -S . \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=Release

  cmake --build build \
    --config Release
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[apkstudio]] · [[BlackArch Tools]]

