---
tool: "silenteye"
build-system: "cmake"
upstream: "https://github.com/achorein/silenteye/"
recipe: "blackarch"
source: "https://github.com/achorein/silenteye/"
license: "GPL3"
blackarch-pkgver: "21.a53a7ff"
---

# Installing silenteye

> A cross-platform application design for an easy use of steganography.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/silenteye) (version `21.a53a7ff`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S silenteye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/achorein/silenteye/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `qca`
  - `qt4`

## Exact build steps from the recipe

```bash
cd $pkgname

  ENABLE_PACK=1 cmake -DCMAKE_INSTALL_PREFIX=/usr .

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[silenteye]] · [[BlackArch Tools]]

