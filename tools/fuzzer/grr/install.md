---
tool: "grr"
build-system: "cmake"
upstream: "https://github.com/trailofbits/grr"
recipe: "blackarch"
source: "https://github.com/trailofbits/grr"
license: "Apache"
blackarch-pkgver: "55.76f6ee9"
---

# Installing grr

> High-throughput fuzzer and emulator of DECREE binaries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grr) (version `55.76f6ee9`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S grr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/trailofbits/grr) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `gflags`
  - `llvm`
  - `radamsa`
  - `xed`
  - `xed-intel`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build
  cmake build -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ \
    -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr ..
  make all
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[grr]] · [[BlackArch Tools]]

