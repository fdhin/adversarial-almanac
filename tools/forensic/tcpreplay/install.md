---
tool: "tcpreplay"
build-system: "autotools"
upstream: "https://tcpreplay.appneta.com"
recipe: "arch-official/extra"
nix-package: "tcpreplay"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/tcpreplay/-/raw/main/PKGBUILD"
version: "4.6.1"
license: "GPL-3.0-only"
---

# Installing tcpreplay

> Tool to replay saved tcpdump files at arbitrary speeds

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/tcpreplay/-/raw/main/PKGBUILD) (version `4.6.1`, license GPL-3.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S tcpreplay
```

## Kali / Debian-based — same package name

```bash
sudo apt install tcpreplay
```

## Nix (works on any distro)

```bash
nix run nixpkgs#tcpreplay    # ad-hoc run, no install
nix profile install nixpkgs#tcpreplay   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://tcpreplay.appneta.com/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `tcpdump`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  export CFLAGS="$CFLAGS -std=gnu99"
  ./configure \
    --prefix=/usr \
    --disable-local-libopts \
    --enable-dynamic-link \
    --enable-shared \
    --with-libdnet \
    --with-tcpdump=/usr/bin/tcpdump \
    ac_cv_header_net_bpf_h=n
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[tcpreplay]] · [[Kali Gap Analysis]]
