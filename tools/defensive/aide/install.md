---
tool: "aide"
build-system: "autotools"
upstream: "https://aide.github.io/"
recipe: "blackarch"
nix-package: "aide"
aur-package: "aide"
source: "https://aide.github.io/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.19.4"
---

# Installing aide

> A file integrity checker and intrusion detection program.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aide) (version `0.19.4`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S aide    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://aide.github.io/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `acl`
  - `curl`
  - `e2fsprogs`
  - `nettle`
  - `pcre2`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure \
    --prefix="/usr" \
    --sysconfdir="/etc" \
    --with-posix-acl \
    --with-xattr \
    --with-zlib \
    --with-e2fsattrs \
    --disable-static \
    --with-curl \
    --with-nettle

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[aide]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#aide    # ad-hoc run, no install
nix profile install nixpkgs#aide   # or persist it
```
