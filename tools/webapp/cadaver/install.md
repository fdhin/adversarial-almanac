---
tool: "cadaver"
build-system: "autotools"
upstream: "https://notroj.github.io/cadaver/"
recipe: "arch-official/extra"
nix-package: "cadaver"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/cadaver/-/raw/main/PKGBUILD"
version: "0.28"
license: "GPL-2.0-only"
---

# Installing cadaver

> command-line WebDAV client

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/cadaver/-/raw/main/PKGBUILD) (version `0.28`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S cadaver
```

## Kali / Debian-based — same package name

```bash
sudo apt install cadaver
```

## Nix (works on any distro)

```bash
nix run nixpkgs#cadaver    # ad-hoc run, no install
nix profile install nixpkgs#cadaver   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/notroj/cadaver) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `neon`

## Exact build steps from the recipe

```bash
cd "$srcdir"/cadaver-$pkgver
#  test -x ./configure || ./autogen.sh
  ./configure --prefix=/usr --with-ssl
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[cadaver]] · [[Kali Gap Analysis]]
