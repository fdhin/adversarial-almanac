---
tool: "netsniff-ng"
build-system: "cmake"
upstream: "http://netsniff-ng.org/"
recipe: "arch-official/extra"
nix-package: "netsniff-ng"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/netsniff-ng/-/raw/main/PKGBUILD"
version: "0.6.9"
license: "GPL-2.0-only"
---

# Installing netsniff-ng

> Linux network packet sniffer toolkit

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/netsniff-ng/-/raw/main/PKGBUILD) (version `0.6.9`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S netsniff-ng
```

## Kali / Debian-based — same package name

```bash
sudo apt install netsniff-ng
```

## Nix (works on any distro)

```bash
nix run nixpkgs#netsniff-ng    # ad-hoc run, no install
nix profile install nixpkgs#netsniff-ng   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/netsniff-ng/netsniff-ng.git
cd netsniff-ng
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `bison`
  - `flex`
  - `geoip`
  - `glibc`
  - `libcli`
  - `libcli.so`
  - `libgcc`
  - `libncursesw.so`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libnetfilter_conntrack`
  - `libnl`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libpcap.so`
  - `libsodium`
  - `liburcu`
  - `ncurses`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}
  NACL_INC_DIR=/usr/include/sodium
  NACL_LIB=sodium
  ./configure \
    --prefix=/usr
  make all Q= CFLAGS="${CFLAGS} ${CPPFLAGS}"
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[netsniff-ng]] · [[Kali Gap Analysis]]
