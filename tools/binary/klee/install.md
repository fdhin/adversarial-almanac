---
tool: "klee"
build-system: "cmake"
upstream: "https://github.com/klee/klee"
recipe: "blackarch"
nix-package: "klee"
aur-package: "klee"
source: "https://github.com/google/googletest.git"
license: "custom:UIUC"
blackarch-pkgver: "2.3"
---

# Installing klee

> A symbolic virtual machine built on top of the LLVM compiler infrastructure.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/klee) (version `2.3`, license custom:UIUC).

## Arch / BlackArch

```bash
sudo pacman -S klee    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/google/googletest.git
cd googletest
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `gperftools`
  - `klee-uclibc`
  - `libcap`
  - `llvm`
  - `llvm-libs`
  - `python`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `z3`

## Exact build steps from the recipe

```bash
cd build

  make $MAKEFLAGS
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[klee]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#klee    # ad-hoc run, no install
nix profile install nixpkgs#klee   # or persist it
```
