---
tool: "vbrute"
build-system: "autotools"
upstream: "https://github.com/nccgroup/vbrute"
recipe: "blackarch"
source: "https://github.com/nccgroup/vbrute"
license: "GPL3"
blackarch-pkgver: "1.11dda8b"
---

# Installing vbrute

> Virtual hosts brute forcer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vbrute) (version `1.11dda8b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S vbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/vbrute) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libevent`

## Exact build steps from the recipe

```bash
cd $pkgname

  autoreconf

  ./configure --prefix=/usr
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[vbrute]] · [[BlackArch Tools]]

