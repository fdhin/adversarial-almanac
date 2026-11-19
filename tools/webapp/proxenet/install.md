---
tool: "proxenet"
build-system: "cmake"
upstream: "https://github.com/hugsy/proxenet"
recipe: "blackarch"
source: "https://github.com/hugsy/proxenet"
license: "GPL2"
blackarch-pkgver: "712.67fc6b5"
---

# Installing proxenet

> THE REAL hacker friendly proxy for web application pentests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proxenet) (version `712.67fc6b5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S proxenet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/hugsy/proxenet) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `lua`
  - `mbedtls`
  - `python2`
  - `python3`
  - `ruby`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr .

  make

  #make -C keys
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`, `/usr/share/proxenet`

Backlinks: [[webapp]] · [[proxenet]] · [[BlackArch Tools]]

