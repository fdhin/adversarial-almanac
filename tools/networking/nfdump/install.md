---
tool: "nfdump"
build-system: "autotools"
upstream: "https://github.com/phaag/nfdump"
recipe: "blackarch"
nix-package: "nfdump"
aur-package: "nfdump"
source: "https://github.com/phaag/nfdump"
license: "BSD"
blackarch-pkgver: "1.6.23"
---

# Installing nfdump

> A set of tools to collect and process netflow data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nfdump) (version `1.6.23`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S nfdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/phaag/nfdump) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`
- **System libraries** (Arch name — translate for your distro):
  - `bison`
  - `flex`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[nfdump]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#nfdump    # ad-hoc run, no install
nix profile install nixpkgs#nfdump   # or persist it
```
