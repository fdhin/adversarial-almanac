---
tool: "zeek-aux"
build-system: "cmake"
upstream: "https://github.com/zeek/zeek-aux"
recipe: "blackarch"
source: "https://github.com/zeek/cmake.git"
license: "custom:unknown"
blackarch-pkgver: "794.f1f4bc5"
---

# Installing zeek-aux

> Handy auxiliary programs related to the use of the Zeek Network Security Monitor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zeek-aux) (version `794.f1f4bc5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S zeek-aux    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/zeek/zeek-aux.git
cd zeek-aux
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `zeek`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix="$pkgdir/usr"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[zeek-aux]] · [[BlackArch Tools]]

