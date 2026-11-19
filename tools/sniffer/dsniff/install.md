---
tool: "dsniff"
build-system: "autotools"
upstream: "https://www.monkey.org/~dugsong/dsniff/"
recipe: "arch-official/extra"
nix-package: "dsniff"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/dsniff/-/raw/main/PKGBUILD"
version: "2.4b1"
license: "BSD"
---

# Installing dsniff

> Various tools to sniff network traffic for cleartext insecurities

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/dsniff/-/raw/main/PKGBUILD) (version `2.4b1`, license BSD).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S dsniff
```

## Kali / Debian-based — same package name

```bash
sudo apt install dsniff
```

## Nix (works on any distro)

```bash
nix run nixpkgs#dsniff    # ad-hoc run, no install
nix profile install nixpkgs#dsniff   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/hackerschoice/dsniff) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `db5.3`
  - `glib2`
  - `glibc`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libnids`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtirpc`
  - `libxmu`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `rpcsvc-proto`

## Exact build steps from the recipe

```bash
cd ${pkgname}-2.4
  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --with-db="$srcdir"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[sniffer]] · [[dsniff]] · [[Kali Gap Analysis]]
