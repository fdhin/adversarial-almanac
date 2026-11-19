---
tool: "bkcrack"
build-system: "cmake"
upstream: "https://github.com/kimci86/bkcrack"
recipe: "blackarch"
nix-package: "bkcrack"
aur-package: "bkcrack"
source: "https://github.com/kimci86/bkcrack"
license: "Zlib"
blackarch-pkgver: "v1.8.1.r53.g980b9ad"
---

# Installing bkcrack

> Crack legacy zip encryption with Biham and Kocher known plaintext attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bkcrack) (version `v1.8.1.r53.g980b9ad`, license Zlib).

## Arch / BlackArch

```bash
sudo pacman -S bkcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/kimci86/bkcrack) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `doxygen`
  - `gcc-libs`
  - `glibc`

## Exact build steps from the recipe

```bash
local cmake_options=(
    -B build
    -S $pkgname
    -W no-dev
    -D CMAKE_BUILD_TYPE=None
    -D CMAKE_INSTALL_PREFIX=/usr
    -D BKCRACK_BUILD_DOC=ON
    -D BKCRACK_BUILD_TESTING=ON
  )
  cmake "${cmake_options[@]}"
  cmake --build build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[bkcrack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bkcrack    # ad-hoc run, no install
nix profile install nixpkgs#bkcrack   # or persist it
```
