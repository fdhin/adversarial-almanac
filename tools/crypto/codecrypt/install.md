---
tool: "codecrypt"
ebuild-package: "codecrypt"
build-system: "autotools"
upstream: "https://github.com/exaexa/${pkgname}"
recipe: "archstrike-pkgbuild"
aur-package: "codecrypt"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/codecrypt"
version: "1.8"
license: "GPL3"
---

# Installing codecrypt

> A GnuPG-like program for encryption and signing that uses only quantum-computer-resistant algorithms

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/codecrypt) (version `1.8`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S codecrypt
```

Also on the AUR: `paru -S codecrypt` (version `1.8-1`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/exaexa/${pkgname}) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`
- **System libraries** (Arch name — translate for your distro):
  - `crypto++`
  - `fftw`
  - `gmp`
  - `libtool`

## Exact build steps from the recipe

```bash
LDFLAGS="${LDFLAGS} -pthread"

    cd "${pkgname}-${pkgver}"
    ./autogen.sh
    ./configure --prefix=/usr
    make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[crypto]] · [[codecrypt]] · [[ArchStrike Gap Analysis]]
