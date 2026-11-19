---
tool: "libparistraceroute"
build-system: "autotools"
upstream: "https://github.com/libparistraceroute/libparistraceroute"
recipe: "blackarch"
source: "https://github.com/libparistraceroute/libparistraceroute"
license: "LGPL"
blackarch-pkgver: "383.9588da5"
---

# Installing libparistraceroute

> A library written in C dedicated to active network measurements with examples, such as paris-ping and paris-traceroute.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/libparistraceroute) (version `383.9588da5`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S libparistraceroute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/libparistraceroute/libparistraceroute) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[libparistraceroute]] · [[BlackArch Tools]]

