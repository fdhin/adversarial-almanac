---
tool: "libtins"
build-system: "cmake"
upstream: "https://github.com/mfontanini/libtins"
recipe: "blackarch"
nix-package: "libtins"
aur-package: "libtins"
source: "https://github.com/mfontanini/libtins"
license: "BSD"
blackarch-pkgver: "1308.3111a90"
---

# Installing libtins

> High-level, multiplatform C++ network packet sniffing and crafting library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libtins) (version `1308.3111a90`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S libtins    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/mfontanini/libtins) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname"

  mkdir -p build
  cd build
  cmake -D CMAKE_INSTALL_PREFIX=/usr -D LIBTINS_ENABLE_CXX11=yes ../
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[libtins]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#libtins    # ad-hoc run, no install
nix profile install nixpkgs#libtins   # or persist it
```
