---
tool: "darkstat"
build-system: "autotools"
upstream: "https://unix4lyfe.org/darkstat/"
recipe: "arch-official/extra"
nix-package: "darkstat"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/darkstat/-/raw/main/PKGBUILD"
version: "3.0.722"
license: "GPL"
---

# Installing darkstat

> network traffic analyzer

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/darkstat/-/raw/main/PKGBUILD) (version `3.0.722`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S darkstat
```

## Kali / Debian-based — same package name

```bash
sudo apt install darkstat
```

## Nix (works on any distro)

```bash
nix run nixpkgs#darkstat    # ad-hoc run, no install
nix profile install nixpkgs#darkstat   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://unix4lyfe.org/darkstat/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libpcap.so`
  - `libz.so`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "${srcdir}/darkstat-${pkgver}"

  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[sniffer]] · [[darkstat]] · [[Kali Gap Analysis]]
