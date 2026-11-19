---
tool: "medusa"
build-system: "autotools"
upstream: "http://www.foofus.net/jmk/medusa/medusa.html"
recipe: "arch-official/extra"
nix-package: "medusa"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/medusa/-/raw/main/PKGBUILD"
version: "2.3"
license: "GPL2"
---

# Installing medusa

> fast, parallel, modular, login brute-forcer for network services

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/medusa/-/raw/main/PKGBUILD) (version `2.3`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S medusa
```

## Kali / Debian-based — same package name

```bash
sudo apt install medusa
```

## Nix (works on any distro)

```bash
nix run nixpkgs#medusa    # ad-hoc run, no install
nix profile install nixpkgs#medusa   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://foofus.net/?page_id=51) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `afpfs-ng`
  - `freerdp`
  - `libssh2`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `postgresql-libs` — Debian libpq-dev / Fedora libpq-devel
  - `subversion`

## Exact build steps from the recipe

```bash
cd ${pkgname}
  ./configure --prefix=/usr \
    CFLAGS="${CFLAGS} $(pkg-config --cflags freerdp2)"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[medusa]] · [[Kali Gap Analysis]]
