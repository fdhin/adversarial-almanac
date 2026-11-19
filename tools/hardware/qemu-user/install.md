---
tool: "qemu-user"
build-system: "other"
upstream: "https://www.qemu.org/"
recipe: "arch-official/extra"
nix-package: "qemu-user"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/qemu-user/-/raw/main/PKGBUILD"
version: "11.1.3"
license: "GPL-2.0-only WITH Linux-syscall-note, GPL-2.0-only WITH Linux-syscall-note OR BSD-2-Clause, GPL-2.0-only WITH Linux-syscall-note OR BSD-3-Clause, GPL-2.0-or-later OR BSD-3-Clause, GPL-2.0-or-later WITH Linux-syscall-note, LGPL-2.1-only OR BSD-3-Clause"
provides: ["qemu-"]
---

# Installing qemu-user

> QEMU user mode emulation (static binaries)

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/qemu-user/-/raw/main/PKGBUILD) (version `11.1.3`, license GPL-2.0-only WITH Linux-syscall-note, GPL-2.0-only WITH Linux-syscall-note OR BSD-2-Clause, GPL-2.0-only WITH Linux-syscall-note OR BSD-3-Clause, GPL-2.0-or-later OR BSD-3-Clause, GPL-2.0-or-later WITH Linux-syscall-note, LGPL-2.1-only OR BSD-3-Clause).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S qemu-user
```

## Kali / Debian-based — same package name

```bash
sudo apt install qemu-user
```

## Nix (works on any distro)

```bash
nix run nixpkgs#qemu-user    # ad-hoc run, no install
nix profile install nixpkgs#qemu-user   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.qemu.org/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
local common_configure_options=(
    --prefix=/usr
    --sysconfdir=/etc
    --libexecdir=/usr/lib/qemu
    --localstatedir=/var
    --docdir=/usr/share/doc/qemu
  )
  local configure_options=(
    "${common_configure_options[@]}"
    --enable-modules
    --enable-sdl
    --enable-slirp
    --enable-tpm
    --enable-rutabaga-gfx
    --smbd=/usr/bin/smbd
    --with-coroutine=ucontext
  )
  local configure_static_options=(
    "${common_configure_options[@]}"
    --enable-attr
    --enable-linux-user
    --enable-tcg
    --disable-bpf
    --disable-bsd-user
    --disable-capstone
    --disable-docs
    --disable-fdt
    --disable-gcrypt
    --disable-gnutls
    --disable-gtk
    --disable-install-blobs
    --disable-kvm
    --disable-libiscsi
    --disable-libnfs
    --disable-libssh
    --disable-linux-io-uring
    --disable-nettle
    --disable-opengl
    --disable-qom-cast-debug
    --disable-sdl
    --disable-system
    --disable-tools
    --disable-tpm
    --disable-vde
    --disable-vhost-crypto
    --disable-vhost-kernel
    --disable-vhost-net
    --disable-vhost-user
    --disable-vnc
    --disable-werror
    --disable-xen
    --disable-zstd
    --static
  )

  (
    cd build-static
    ../$pkgbase-$pkgver/configure "${configure_static_options[@]}"
    ninja
  )

  # Build only minimal debug info to reduce size
  CFLAGS+=' -g1'
  CXXFLAGS+=' -g1'

  (
    cd build
    ../$pkgbase-$pkgver/configure "${configure_options[@]}"
    ninja
  )
```

## Installed layout (per recipe)

- Commands: `/usr/bin/qemu-`
- Data: `/usr/share/man`

Backlinks: [[hardware]] · [[qemu-user]] · [[Kali Gap Analysis]]
