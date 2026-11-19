---
tool: "lvm2"
build-system: "autotools"
upstream: "https://sourceware.org/lvm2/"
recipe: "arch-official/core"
nix-package: "lvm2"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/lvm2/-/raw/main/PKGBUILD"
version: "2.03.44"
license: "GPL-2.0-only, LGPL-2.1-only"
---

# Installing lvm2

> Linux Logical Volume Manager

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/lvm2/-/raw/main/PKGBUILD) (version `2.03.44`, license GPL-2.0-only, LGPL-2.1-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S lvm2
```

## Kali / Debian-based — same package name

```bash
sudo apt install lvm2
```

## Nix (works on any distro)

```bash
nix run nixpkgs#lvm2    # ad-hoc run, no install
nix profile install nixpkgs#lvm2   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://gitlab.com/lvmteam/lvm2.git?signed
cd lvm2.git?signed
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libaio`
  - `systemd`
  - `thin-provisioning-tools`

## Exact build steps from the recipe

```bash
# build system requires bash:
  # https://www.redhat.com/archives/linux-lvm/2020-January/msg00004.html
  # https://www.gnu.org/software/autoconf/manual/autoconf-2.69/html_node/Defining-Variables.html
  export CONFIG_SHELL=/bin/bash

  cd lvm2/

  ./configure \
    CONFIG_SHELL=/bin/bash \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --sysconfdir=/etc \
    --localstatedir=/var \
    --enable-cmdlib \
    --enable-dmeventd \
    --enable-lvmpolld \
    --enable-pkgconfig \
    --enable-readline \
    --enable-udev_rules \
    --enable-udev_sync \
    --enable-write_install \
    --with-cache=internal \
    --with-default-dm-run-dir=/run \
    --with-default-locking-dir=/run/lock/lvm \
    --with-default-pid-dir=/run \
    --with-default-run-dir=/run/lvm \
    --with-libexecdir=/usr/lib/lvm2 \
    --with-systemdsystemunitdir=/usr/lib/systemd/system \
    --with-thin=internal \
    --with-udev-prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[lvm2]] · [[Kali Gap Analysis]]
