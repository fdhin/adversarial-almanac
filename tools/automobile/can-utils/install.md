---
tool: "can-utils"
build-system: "cmake"
upstream: "https://github.com/linux-can/can-utils"
recipe: "blackarch"
nix-package: "can-utils"
aur-package: "can-utils"
source: "https://github.com/linux-can/can-utils"
license: "GPL2"
blackarch-pkgver: "1304.834ceef"
---

# Installing can-utils

> Linux-CAN / SocketCAN user space applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/can-utils) (version `1304.834ceef`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S can-utils    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/linux-can/can-utils) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake -DCMAKE_INSTALL_PREFIX=/usr .
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automobile]] · [[can-utils]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#can-utils    # ad-hoc run, no install
nix profile install nixpkgs#can-utils   # or persist it
```
