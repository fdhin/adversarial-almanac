---
tool: "bruteforce-salted-openssl"
build-system: "autotools"
upstream: "https://github.com/glv2/bruteforce-salted-openssl"
recipe: "blackarch"
nix-package: "bruteforce-salted-openssl"
source: "https://github.com/glv2/bruteforce-salted-openssl"
license: "GPL3"
blackarch-pkgver: "64.30b272e"
---

# Installing bruteforce-salted-openssl

> Try to find the password of a file that was encrypted with the 'openssl' command.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bruteforce-salted-openssl) (version `64.30b272e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bruteforce-salted-openssl    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/glv2/bruteforce-salted-openssl) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
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
- Data: `/usr/share/man`

Backlinks: [[cracker]] · [[bruteforce-salted-openssl]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#bruteforce-salted-openssl    # ad-hoc run, no install
nix profile install nixpkgs#bruteforce-salted-openssl   # or persist it
```
