---
tool: "afflib"
build-system: "autotools"
upstream: "https://github.com/sshock/AFFLIBv3"
recipe: "blackarch"
nix-package: "afflib"
aur-package: "afflib"
source: "https://github.com/sshock/AFFLIBv3"
license: "custom:unknown"
blackarch-pkgver: "3.7.22"
---

# Installing afflib

> An extensible open format for the storage of disk images and related forensic information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/afflib) (version `3.7.22`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S afflib    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/sshock/AFFLIBv3) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `expat`
  - `fuse2`
  - `fuse3`
  - `ncurses`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `readline`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  sh bootstrap.sh

  if [[ "$CARCH" == "aarch64" ]];
  then
    ./configure \
      --build=arm \
      --prefix=/usr \
      --sysconfdir=/etc \
      --enable-fuse \
      --enable-s3
  else
    ./configure \
      --prefix=/usr \
      --sysconfdir=/etc \
      --enable-fuse \
      --enable-s3
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[afflib]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#afflib    # ad-hoc run, no install
nix profile install nixpkgs#afflib   # or persist it
```
