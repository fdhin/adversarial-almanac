---
tool: "libfvde"
build-system: "autotools"
upstream: "https://github.com/libyal/libfvde"
recipe: "blackarch"
source: "https://github.com/libyal/libfvde"
license: "LGPL3"
blackarch-pkgver: "223.5551263"
---

# Installing libfvde

> Library and tools to access FileVault Drive Encryption (FVDE) encrypted volumes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libfvde) (version `223.5551263`, license LGPL3).

## Arch / BlackArch

```bash
sudo pacman -S libfvde    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/libyal/libfvde) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `gcc`, `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `binutils`
  - `libtool`
  - `libxml2` — Debian libxml2-dev / Fedora libxml2-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[libfvde]] · [[BlackArch Tools]]

