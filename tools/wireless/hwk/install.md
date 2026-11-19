---
tool: "hwk"
build-system: "make"
upstream: "http://www.nullsecurity.net/"
recipe: "blackarch"
source: "http://www.nullsecurity.net/"
license: "GPL-2.0-only"
blackarch-pkgver: "0.4"
provides: ["hawk", "hwk-eagle"]
---

# Installing hwk

> Collection of packet crafting and wireless network flooding tools

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hwk) (version `0.4`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S hwk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.nullsecurity.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make clean && make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/hawk`, `/usr/bin/hwk-eagle`

Backlinks: [[wireless]] · [[hwk]] · [[BlackArch Tools]]

