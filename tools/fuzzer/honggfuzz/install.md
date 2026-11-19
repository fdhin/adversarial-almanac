---
tool: "honggfuzz"
build-system: "make"
upstream: "https://code.google.com/p/honggfuzz/"
recipe: "blackarch"
nix-package: "honggfuzz"
source: "https://code.google.com/p/honggfuzz/"
license: "Apache"
blackarch-pkgver: "4182.940b958d"
---

# Installing honggfuzz

> A general-purpose fuzzer with simple, command-line interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/honggfuzz) (version `4182.940b958d`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S honggfuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://code.google.com/p/honggfuzz/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `binutils`
  - `libunwind`

## Exact build steps from the recipe

```bash
cd $pkgname

  CFLAGS="" make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[honggfuzz]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#honggfuzz    # ad-hoc run, no install
nix profile install nixpkgs#honggfuzz   # or persist it
```
