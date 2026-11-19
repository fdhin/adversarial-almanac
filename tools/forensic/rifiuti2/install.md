---
tool: "rifiuti2"
build-system: "cmake"
upstream: "https://github.com/abelcheung/rifiuti2"
recipe: "blackarch"
aur-package: "rifiuti2"
source: "https://github.com/abelcheung/rifiuti2"
license: "custom:BSD"
blackarch-pkgver: "0.8.2"
kali-package: "rifiuti2"
kali-version: "0.8.2-1"
---

# Installing rifiuti2

> A rewrite of rifiuti, a great tool from Foundstone folks for analyzing Windows Recycle Bin INFO2 file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rifiuti2) (version `0.8.2`, license custom:BSD).

## Arch / BlackArch

```bash
sudo pacman -S rifiuti2    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install rifiuti2    # Kali package version 0.8.2-1
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/abelcheung/rifiuti2) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glib2`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  cmake -DCMAKE_INSTALL_PREFIX=/usr .

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[rifiuti2]] · [[BlackArch Tools]]

