---
tool: "sipsak"
build-system: "autotools"
upstream: "https://github.com/nils-ohlmeier/sipsak/releases"
recipe: "blackarch"
nix-package: "sipsak"
source: "https://github.com/nils-ohlmeier/sipsak/releases"
license: "GPL"
blackarch-pkgver: "0.9.8.1"
kali-package: "sipsak"
kali-version: "0.9.8.1-1.1"
---

# Installing sipsak

> A small command line tool for developers and administrators of Session Initiation Protocol (SIP) applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sipsak) (version `0.9.8.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S sipsak    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sipsak    # Kali package version 0.9.8.1-1.1
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/nils-ohlmeier/sipsak/releases) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `c-ares`
  - `gnutls`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  autoreconf --install

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[sipsak]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sipsak    # ad-hoc run, no install
nix profile install nixpkgs#sipsak   # or persist it
```
