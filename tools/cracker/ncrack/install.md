---
tool: "ncrack"
build-system: "autotools"
upstream: "https://nmap.org/ncrack/"
recipe: "arch-official/extra"
nix-package: "ncrack"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/ncrack/-/raw/main/PKGBUILD"
version: "0.7"
license: "GPL2"
---

# Installing ncrack

> High-speed network authentication cracking tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/ncrack/-/raw/main/PKGBUILD) (version `0.7`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S ncrack
```

## Kali / Debian-based — same package name

```bash
sudo apt install ncrack
```

## Nix (works on any distro)

```bash
nix run nixpkgs#ncrack    # ad-hoc run, no install
nix profile install nixpkgs#ncrack   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://nmap.org/ncrack/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  CFLAGS+=' -Wno-int-conversion -Wno-implicit-function-declaration -fcommon' # https://wiki.gentoo.org/wiki/Gcc_10_porting_notes/fno_common
  ./configure \
    --prefix=/usr \
    --without-openssl-header-check
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[ncrack]] · [[Kali Gap Analysis]]
