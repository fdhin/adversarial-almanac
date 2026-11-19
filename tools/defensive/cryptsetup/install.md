---
tool: "cryptsetup"
build-system: "autotools"
upstream: "https://gitlab.com/cryptsetup/cryptsetup/"
recipe: "arch-official/core"
nix-package: "cryptsetup"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/cryptsetup/-/raw/main/PKGBUILD"
version: "2.8.8"
license: "GPL-2.0-or-later"
---

# Installing cryptsetup

> disk encryption support - startup scripts

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/cryptsetup/-/raw/main/PKGBUILD) (version `2.8.8`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S cryptsetup
```

## Kali / Debian-based — same package name

```bash
sudo apt install cryptsetup
```

## Nix (works on any distro)

```bash
nix run nixpkgs#cryptsetup    # ad-hoc run, no install
nix profile install nixpkgs#cryptsetup   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://gitlab.com/cryptsetup/cryptsetup) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `asciidoctor`
  - `device-mapper`
  - `glibc`
  - `json-c`
  - `libblkid.so`
  - `libcrypto.so`
  - `libdevmapper.so`
  - `libjson-c.so`
  - `libuuid.so`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `popt`
  - `util-linux`
  - `util-linux-libs`

## Exact build steps from the recipe

```bash
cd "${srcdir}"/$pkgname-${pkgver}

  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --disable-ssh-token \
    --disable-static
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[cryptsetup]] · [[Kali Gap Analysis]]
