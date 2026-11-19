---
tool: "libbde"
build-system: "autotools"
upstream: "https://github.com/libyal/libbde"
recipe: "blackarch"
nix-package: "libbde"
aur-package: "libbde"
source: "https://github.com/libyal/libbde"
license: "LGPL-3.0-or-later"
blackarch-pkgver: "20260916.r2.gdd48d47"
---

# Installing libbde

> A library to access the BitLocker Drive Encryption (BDE) format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libbde) (version `20260916.r2.gdd48d47`, license LGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S libbde    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/libyal/libbde) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bison`
  - `flex`
  - `gettext`
  - `libtool`
  - `pkg-config`
  - `python`
  - `python2`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./synclibs.sh
  ./autogen.sh
  ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --enable-python2 \
    --enable-python

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[libbde]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#libbde    # ad-hoc run, no install
nix profile install nixpkgs#libbde   # or persist it
```
