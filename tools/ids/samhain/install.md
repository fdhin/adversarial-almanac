---
tool: "samhain"
ebuild-package: "samhain"
build-system: "autotools"
upstream: "http://la-samhna.de/${pkgname}"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/samhain"
version: "4.4.6"
license: "GPL"
---

# Installing samhain

> File integrity / intrusion detection system

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/samhain) (version `4.4.6`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S samhain
```

The same PKGBUILD is usually on the AUR (`paru -S samhain`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://la-samhna.de/${pkgname}) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `audit`
  - `procps-ng`
  - `systemd`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"
    ./configure \
        --prefix=/usr \
        --sbindir=/usr/bin \
        --localstatedir=/var \
        --sysconfdir=/etc \
        --disable-network \
        --enable-suidcheck \
        --enable-login-watch \
        --enable-ptrace \
        --enable-mounts-check \
        --enable-process-check \
        --enable-port-check \
        --disable-asm
    make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[ids]] · [[samhain]] · [[ArchStrike Gap Analysis]]
