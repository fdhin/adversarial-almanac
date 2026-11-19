---
tool: "eresi"
build-system: "autotools"
upstream: "https://github.com/thorkill/eresi"
recipe: "blackarch"
nix-package: "eresi"
source: "https://github.com/thorkill/eresi"
license: "GPL2"
blackarch-pkgver: "1291.4769c175"
---

# Installing eresi

> The ERESI Reverse Engineering Software Interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eresi) (version `1291.4769c175`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S eresi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/thorkill/eresi) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  export DESTDIR="$pkgdir/usr"

  ./configure --enable-32-64

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[eresi]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#eresi    # ad-hoc run, no install
nix profile install nixpkgs#eresi   # or persist it
```
