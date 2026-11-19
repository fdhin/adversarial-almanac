---
tool: "derrick"
ebuild-package: "derrick"
build-system: "autotools"
upstream: "https://github.com/rieck/derrick"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/derrick"
version: "0.3"
license: "custom"
---

# Installing derrick

> Simple tool for recording data streams of TCP and UDP traffic.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/derrick) (version `0.3`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S derrick
```

The same PKGBUILD is usually on the AUR (`paru -S derrick`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/rieck/derrick) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libnids`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver
  ./bootstrap
  ./configure --prefix=$pkgdir/usr/share/$pkgname
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[sniffer]] · [[derrick]] · [[ArchStrike Gap Analysis]]
