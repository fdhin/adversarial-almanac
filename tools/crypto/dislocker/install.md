---
tool: "dislocker"
build-system: "cmake"
upstream: "https://github.com/Aorimn/dislocker"
recipe: "blackarch"
nix-package: "dislocker"
aur-package: "dislocker"
source: "https://github.com/Aorimn/dislocker"
license: "GPL2"
blackarch-pkgver: "v0.7.4.r0.g21ef6e5"
---

# Installing dislocker

> Read BitLocker encrypted volumes under Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dislocker) (version `v0.7.4.r0.g21ef6e5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dislocker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/Aorimn/dislocker) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `fuse`
  - `mbedtls`
  - `ruby`

## Exact build steps from the recipe

```bash
cd $pkgname

  # Fix v0.7 upstream path error
  sed -i 's/DIS_MAN \${PROJECT_SOURCE_DIR}/DIS_MAN ../' src/CMakeLists.txt

  cmake -DCMAKE_INSTALL_PREFIX=/usr \
        -Dlibdir=/usr/lib \
        -DCMAKE_POLICY_VERSION_MINIMUM=3.5 \
        -D WARN_FLAGS:STRING="-Wall -Wextra" \
        .
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[dislocker]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dislocker    # ad-hoc run, no install
nix profile install nixpkgs#dislocker   # or persist it
```
