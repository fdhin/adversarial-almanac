---
tool: "arptools"
build-system: "cmake"
upstream: "https://github.com/Lab-Zjt/ARPTools"
recipe: "blackarch"
source: "https://github.com/Lab-Zjt/ARPTools"
license: "custom:unknown"
blackarch-pkgver: "13.41cdb23"
---

# Installing arptools

> A simple tool about ARP broadcast, ARP attack, and data transmission.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/arptools) (version `13.41cdb23`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S arptools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/Lab-Zjt/ARPTools.git
cd ARPTools
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake .
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[arptools]] · [[BlackArch Tools]]

