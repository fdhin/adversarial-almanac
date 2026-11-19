---
tool: "fragroute"
ebuild-package: "fragroute"
build-system: "autotools"
upstream: "http://www.monkey.org/~dugsong/fragroute/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/fragroute"
version: "1.2"
license: "GPL"
---

# Installing fragroute

> A network intrusion detection evasion toolkit

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/fragroute) (version `1.2`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S fragroute
```

The same PKGBUILD is usually on the AUR (`paru -S fragroute`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.monkey.org/~dugsong/fragroute/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libevent`
  - `libevent-static`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "${pkgname}-${pkgver}"
  CPPFLAGS= ./configure --prefix=/usr --sbindir=/usr/bin --mandir=/usr/share/man --sysconfdir=/etc
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[scanner]] · [[fragroute]] · [[ArchStrike Gap Analysis]]
