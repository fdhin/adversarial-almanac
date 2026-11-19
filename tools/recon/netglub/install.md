---
tool: "netglub"
ebuild-package: "netglub"
build-system: "make"
upstream: "http://www.netglub.org"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/netglub"
version: "1.0"
license: "GPL"
---

# Installing netglub

> The really open source information gathering tool

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/netglub) (version `1.0`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S netglub
```

The same PKGBUILD is usually on the AUR (`paru -S netglub`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.netglub.org) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gdb`
  - `graphviz`
  - `mariadb`
  - `python2`
  - `qt4`
  - `sqlitebrowser`

## Exact build steps from the recipe

```bash
# _FORTIFY_SOURCE causes errors maybe.
  unset CPPFLAGS CFLAGS CXXFLAGS LDFLAGS
  cd "$srcdir/netglub-$pkgver/master"
  qmake-qt4
  make
  cd "$srcdir/netglub-$pkgver/slave"
  qmake-qt4
  make
  cd "$srcdir/netglub-$pkgver/qng"
  qmake-qt4
  scrub=$(awk '/LIBS.+=/' Makefile)
  sed -i 's|'"$scrub"'|&-lz|' Makefile
  sed -i '/^INCPATH/s|=|= -I/usr/include/graphviz|' Makefile
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[netglub]] · [[ArchStrike Gap Analysis]]
