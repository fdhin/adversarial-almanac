---
tool: "dhcpoptinj"
build-system: "cmake"
upstream: "https://github.com/misje/dhcpoptinj"
recipe: "blackarch"
source: "https://github.com/misje/dhcpoptinj"
license: "GPL3"
blackarch-pkgver: "123.58a12c6"
---

# Installing dhcpoptinj

> DHCP option injector.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dhcpoptinj) (version `123.58a12c6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dhcpoptinj    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/misje/dhcpoptinj) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libnetfilter_queue`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake ..

  make -j1
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[dhcpoptinj]] · [[BlackArch Tools]]

