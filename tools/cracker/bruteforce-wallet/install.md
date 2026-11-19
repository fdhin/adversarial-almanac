---
tool: "bruteforce-wallet"
build-system: "autotools"
upstream: "https://github.com/glv2/bruteforce-wallet"
recipe: "blackarch"
nix-package: "bruteforce-wallet"
source: "https://github.com/glv2/bruteforce-wallet"
license: "GPL3"
blackarch-pkgver: "43.648d7d7"
---

# Installing bruteforce-wallet

> Try to find the password of an encrypted Peercoin (or Bitcoin,Litecoin, etc...) wallet file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bruteforce-wallet) (version `43.648d7d7`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bruteforce-wallet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/glv2/bruteforce-wallet) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `db5.3`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[bruteforce-wallet]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bruteforce-wallet    # ad-hoc run, no install
nix profile install nixpkgs#bruteforce-wallet   # or persist it
```
