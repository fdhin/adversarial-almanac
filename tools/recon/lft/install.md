---
tool: "lft"
build-system: "autotools"
upstream: "http://pwhois.org/lft/"
recipe: "blackarch"
aur-package: "lft"
source: "http://pwhois.org/lft/"
license: "custom:unknown"
blackarch-pkgver: "3.91"
---

# Installing lft

> A layer four traceroute implementing numerous other features.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lft) (version `3.91`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S lft    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://pwhois.org/lft/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --prefix=/usr --build=arm
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[lft]] · [[BlackArch Tools]]

