---
tool: "phantap"
build-system: "cmake"
upstream: "https://github.com/nccgroup/phantap"
recipe: "blackarch"
source: "https://github.com/nccgroup/phantap"
license: "MIT"
blackarch-pkgver: "65.e8d10ea"
---

# Installing phantap

> An 'invisible' network tap aimed at red teams.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phantap) (version `65.e8d10ea`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S phantap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/phantap) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  cmake -DCMAKE_INSTALL_PREFIX=/usr .

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[phantap]] · [[BlackArch Tools]]

