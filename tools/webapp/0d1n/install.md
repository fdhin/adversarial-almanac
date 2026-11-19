---
tool: "0d1n"
build-system: "make"
upstream: "https://github.com/CoolerVoid/0d1n"
recipe: "blackarch"
source: "https://github.com/CoolerVoid/0d1n"
license: "GPL3"
blackarch-pkgver: "0d1n_v37.r6.g2d723ae"
provides: ["0d1n_view"]
---

# Installing 0d1n

> Web security tool to make fuzzing at HTTP inputs, made in C with libCurl.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/0d1n) (version `0d1n_v37.r6.g2d723ae`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S 0d1n    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/CoolerVoid/0d1n) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Exact build steps from the recipe

```bash
cd $_pkgname

  make

  cd 0d1n_viewer

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/0d1n_view`

Backlinks: [[webapp]] · [[0d1n]] · [[BlackArch Tools]]

