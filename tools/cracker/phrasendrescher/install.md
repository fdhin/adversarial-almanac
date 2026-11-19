---
tool: "phrasendrescher"
build-system: "autotools"
upstream: "http://www.leidecker.info/projects/phrasendrescher/"
recipe: "blackarch"
nix-package: "phrasendrescher"
aur-package: "phrasendrescher"
source: "http://www.leidecker.info/projects/phrasendrescher/"
license: "BSD"
blackarch-pkgver: "1.2.2c"
provides: ["pd"]
---

# Installing phrasendrescher

> A modular and multi processing pass phrase cracking tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/phrasendrescher) (version `1.2.2c`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S phrasendrescher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.leidecker.info/projects/phrasendrescher/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gpgme`
  - `libssh2`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --with-plugins
  else
    ./configure --build=arm --prefix=/usr --with-plugins
  fi

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/pd`
- Data: `/usr/share/man`

Backlinks: [[cracker]] · [[phrasendrescher]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#phrasendrescher    # ad-hoc run, no install
nix profile install nixpkgs#phrasendrescher   # or persist it
```
