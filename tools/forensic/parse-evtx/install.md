---
tool: "parse-evtx"
build-system: "cmake"
upstream: "https://github.com/KasperskyLab/ForensicsTools"
recipe: "blackarch"
source: "https://github.com/KasperskyLab/ForensicsTools"
license: "GPL"
blackarch-pkgver: "3.a4b02b9"
---

# Installing parse-evtx

> A tool to parse the Windows XML Event Log (EVTX) format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/parse-evtx) (version `3.a4b02b9`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S parse-evtx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/KasperskyLab/ForensicsTools.git
cd ForensicsTools
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake ./SOURCES/ -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release \
    -DBUILD_TESTS=OFF

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[parse-evtx]] · [[BlackArch Tools]]

