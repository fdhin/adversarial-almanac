---
tool: "imhex"
build-system: "cmake"
upstream: "https://github.com/WerWolv/ImHex"
recipe: "blackarch"
nix-package: "imhex"
aur-package: "imhex"
source: "https://github.com/WerWolv/ImHex"
license: "GPL-2.0-or-later"
blackarch-pkgver: "nightly.r0.g34483cb"
---

# Installing imhex

> A Hex Editor for Reverse Engineers, Programmers and people that value their eye sight when working at 3 AM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/imhex) (version `nightly.r0.g34483cb`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S imhex    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/WerWolv/ImHex.git
cd ImHex
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `capstone`
  - `curl`
  - `dbus`
  - `file`
  - `fmt`
  - `freetype2`
  - `glfw`
  - `glm`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `librsvg`
  - `libssh2`
  - `lld`
  - `llvm`
  - `llvm-libs`
  - `mbedtls`
  - `nlohmann-json`
  - `python`
  - `xdg-desktop-portal-gtk`
  - `yara`

## Exact build steps from the recipe

```bash
cd $pkgname

  git submodule update --init --recursive
  cmake -B build -Wno-dev \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_SKIP_RPATH=ON \
    -DUSE_SYSTEM_LLVM=ON \
    -DUSE_SYSTEM_YARA=ON \
    -DUSE_SYSTEM_FMT=ON \
    -DUSE_SYSTEM_CURL=ON \
    -DUSE_SYSTEM_NLOHMANN_JSON=ON \
    -DUSE_SYSTEM_CAPSTONE=OFF
  make -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[imhex]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#imhex    # ad-hoc run, no install
nix profile install nixpkgs#imhex   # or persist it
```
