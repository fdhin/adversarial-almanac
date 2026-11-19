---
tool: "imagejs"
build-system: "cmake"
upstream: "https://github.com/jklmnn/imagejs"
recipe: "blackarch"
source: "https://github.com/jklmnn/imagejs"
license: "GPL3"
blackarch-pkgver: "56.a442f94"
---

# Installing imagejs

> Small tool to package javascript into a valid image file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/imagejs) (version `56.a442f94`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S imagejs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/jklmnn/imagejs) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake .
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[imagejs]] · [[BlackArch Tools]]

