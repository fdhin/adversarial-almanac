---
tool: "loadlibrary"
build-system: "make"
upstream: "https://github.com/taviso/loadlibrary"
recipe: "blackarch"
nix-package: "loadlibrary"
source: "https://github.com/taviso/loadlibrary"
license: "GPL2"
blackarch-pkgver: "104.c40033b"
provides: ["mpclient"]
---

# Installing loadlibrary

> Porting Windows Dynamic Link Libraries to Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/loadlibrary) (version `104.c40033b`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S loadlibrary    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/taviso/loadlibrary) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `cabextract`
  - `lib32-gcc-libs`
  - `lib32-glibc`
  - `readline`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/mpclient`

Backlinks: [[binary]] · [[loadlibrary]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#loadlibrary    # ad-hoc run, no install
nix profile install nixpkgs#loadlibrary   # or persist it
```
