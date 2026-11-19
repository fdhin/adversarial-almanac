---
tool: "siege"
build-system: "autotools"
upstream: "https://www.joedog.org/siege-home/"
recipe: "arch-official/extra"
nix-package: "siege"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/siege/-/raw/main/PKGBUILD"
version: "4.2.0"
license: "GPL-3.0-or-later"
---

# Installing siege

> HTTP regression testing and benchmarking utility

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/siege/-/raw/main/PKGBUILD) (version `4.2.0`, license GPL-3.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S siege
```

## Kali / Debian-based — same package name

```bash
sudo apt install siege
```

## Nix (works on any distro)

```bash
nix run nixpkgs#siege    # ad-hoc run, no install
nix profile install nixpkgs#siege   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.joedog.org/JoeDog/Siege) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver
  ./configure --prefix=/usr --sysconfdir=/etc --mandir=/usr/share/man
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[scanner]] · [[siege]] · [[Kali Gap Analysis]]
