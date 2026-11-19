---
tool: "pev"
build-system: "make"
upstream: "http://pev.sourceforge.net/"
recipe: "blackarch"
nix-package: "pev"
source: "http://pev.sourceforge.net/"
license: "GPL3"
blackarch-pkgver: "0.81"
---

# Installing pev

> Command line based tool for PE32/PE32+ file analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pev) (version `0.81`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pev    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://pev.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pcre`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[pev]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pev    # ad-hoc run, no install
nix profile install nixpkgs#pev   # or persist it
```
