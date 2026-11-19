---
tool: "retdec"
build-system: "cmake"
upstream: "https://retdec.com/"
recipe: "blackarch"
nix-package: "retdec"
aur-package: "retdec"
source: "https://retdec.com/"
license: "MIT"
blackarch-pkgver: "2175.94505857"
---

# Installing retdec

> Retargetable machine-code decompiler based on LLVM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/retdec) (version `2175.94505857`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S retdec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://retdec.com/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `cmake`, `gcc`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `bc`
  - `bison`
  - `doxygen`
  - `flex`
  - `graphviz`
  - `libtool`
  - `m4`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `perl`
  - `python`
  - `upx`
  - `wget`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build

  cmake .. -DCMAKE_INSTALL_PREFIX="$pkgdir/usr"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[retdec]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#retdec    # ad-hoc run, no install
nix profile install nixpkgs#retdec   # or persist it
```
