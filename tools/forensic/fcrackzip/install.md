---
tool: "fcrackzip"
build-system: "autotools"
upstream: "http://oldhome.schmorp.de/marc/$pkgname.html"
recipe: "arch-official/extra"
nix-package: "fcrackzip"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/fcrackzip/-/raw/main/PKGBUILD"
version: "1.0"
license: "GPL-2.0-or-later"
provides: ["fzipinfo", "zipinfo"]
---

# Installing fcrackzip

> password cracker for zip archives

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/fcrackzip/-/raw/main/PKGBUILD) (version `1.0`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S fcrackzip
```

## Kali / Debian-based — same package name

```bash
sudo apt install fcrackzip
```

## Nix (works on any distro)

```bash
nix run nixpkgs#fcrackzip    # ad-hoc run, no install
nix profile install nixpkgs#fcrackzip   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://oldhome.schmorp.de/marc/fcrackzip.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"
  export CFLAGS="${CFLAGS} -Wno-error=implicit-int"
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/fzipinfo`, `/usr/bin/zipinfo`

Backlinks: [[forensic]] · [[fcrackzip]] · [[Kali Gap Analysis]]
