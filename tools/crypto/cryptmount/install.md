---
tool: "cryptmount"
ebuild-package: "cryptmount"
build-system: "autotools"
upstream: "http://cryptmount.sourceforge.net/"
recipe: "archstrike-pkgbuild"
aur-package: "cryptmount"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cryptmount"
version: "6.2.0"
license: "GPL"
---

# Installing cryptmount

> Utility which allows an ordinary user to mount an encrypted file system

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cryptmount) (version `6.2.0`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S cryptmount
```

Also on the AUR: `paru -S cryptmount` (version `6.4.0-1`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://cryptmount.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `cryptsetup`

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver
  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --sysconfdir=/etc \
    --with-libgcrypt \
    --with-systemd
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/cryptmount`

Backlinks: [[crypto]] · [[cryptmount]] · [[ArchStrike Gap Analysis]]
