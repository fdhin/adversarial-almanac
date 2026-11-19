---
tool: "redasm"
build-system: "cmake"
upstream: "https://github.com/REDasmOrg/REDasm"
recipe: "blackarch"
source: "https://github.com/REDasmOrg/REDasm"
license: "GPL3"
blackarch-pkgver: "1910.c49aeeff"
---

# Installing redasm

> Interactive, multiarchitecture disassembler written in C++ using Qt5 as UI Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redasm) (version `1910.c49aeeff`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S redasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/REDasmOrg/REDasm.git
cd REDasm
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `qt5-base`
  - `qt5-webengine`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build

  cmake -DCMAKE_BUILD_TYPE=Release ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[redasm]] · [[BlackArch Tools]]

