---
tool: "shellinabox"
build-system: "autotools"
upstream: "https://github.com/shellinabox/shellinabox"
recipe: "blackarch"
source: "https://github.com/shellinabox/shellinabox"
license: "GPL-2.0-only"
blackarch-pkgver: "v2.21.r0.g4f0ecc3"
---

# Installing shellinabox

> Implements a web server that can export arbitrary command line tools to a web based terminal emulator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shellinabox) (version `v2.21.r0.g4f0ecc3`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S shellinabox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/shellinabox/shellinabox) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl-1.0`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  unset CPPFLAGS
  autoreconf -i
  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[shellinabox]] · [[BlackArch Tools]]

