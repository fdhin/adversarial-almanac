---
tool: "procdump"
build-system: "cmake"
upstream: "https://github.com/Microsoft/ProcDump-for-Linux"
recipe: "blackarch"
nix-package: "procdump"
aur-package: "procdump"
source: "https://github.com/Microsoft/ProcDump-for-Linux"
license: "MIT"
blackarch-pkgver: "3.5.2.r5.gc19dbb6"
---

# Installing procdump

> Generate coredumps based off performance triggers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/procdump) (version `3.5.2.r5.gc19dbb6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S procdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/Microsoft/ProcDump-for-Linux.git
cd ProcDump-for-Linux
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bpf`
  - `clang`
  - `gdb`
  - `libbpf`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build
  cmake \
    -DCMAKE_INSTALL_PREFIX=/usr \
    ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[binary]] · [[procdump]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#procdump    # ad-hoc run, no install
nix profile install nixpkgs#procdump   # or persist it
```
