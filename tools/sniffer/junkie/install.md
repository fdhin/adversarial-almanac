---
tool: "junkie"
build-system: "autotools"
upstream: "https://github.com/securactive/junkie"
recipe: "blackarch"
nix-package: "junkie"
source: "https://github.com/securactive/junkie"
license: "GPL3"
blackarch-pkgver: "28.83a6faf"
---

# Installing junkie

> A modular packet sniffer and analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/junkie) (version `28.83a6faf`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S junkie    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/securactive/junkie) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `doxygen`
  - `guile`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl-1.0`
  - `p0f`
  - `rlwrap`

## Exact build steps from the recipe

```bash
cd $pkgname

  export LIBS="-ldl"

  sh autogen.sh

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[junkie]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#junkie    # ad-hoc run, no install
nix profile install nixpkgs#junkie   # or persist it
```
