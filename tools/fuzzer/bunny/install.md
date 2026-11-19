---
tool: "bunny"
build-system: "make"
upstream: "https://code.google.com/p/bunny-the-fuzzer/"
recipe: "blackarch"
nix-package: "bunny"
source: "https://code.google.com/p/bunny-the-fuzzer/"
license: "GPL"
blackarch-pkgver: "0.93"
---

# Installing bunny

> A closed loop, high-performance, general purpose protocol-blind fuzzer for C programs.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bunny) (version `0.93`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bunny    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://code.google.com/p/bunny-the-fuzzer/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[bunny]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bunny    # ad-hoc run, no install
nix profile install nixpkgs#bunny   # or persist it
```
