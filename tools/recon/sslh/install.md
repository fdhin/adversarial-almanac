---
tool: "sslh"
build-system: "autotools"
upstream: "https://www.rutschle.net/tech/sslh/README.html"
recipe: "arch-official/extra"
nix-package: "sslh"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/sslh/-/raw/main/PKGBUILD"
version: "2.3.1"
license: "GPL-2.0-only"
provides: ["sslh", "sslh-ev", "sslh-fork", "sslh-select"]
---

# Installing sslh

> Applicative protocol multiplexer

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/sslh/-/raw/main/PKGBUILD) (version `2.3.1`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S sslh
```

## Kali / Debian-based — same package name

```bash
sudo apt install sslh
```

## Nix (works on any distro)

```bash
nix run nixpkgs#sslh    # ad-hoc run, no install
nix profile install nixpkgs#sslh   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/yrutschle/sslh.git
cd sslh
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libbsd`
  - `libcap`
  - `libconfig`
  - `libev`
  - `pcre2`
  - `systemd`
  - `systemd-libs`

## Exact build steps from the recipe

```bash
cd $pkgname
  ./configure --prefix=/usr --bindir=/usr/bin
  make \
    VERSION=\"$pkgver-$pkgrel\" \
    USELIBCAP=1 \
    USESYSTEMD=1 \
    USELIBBSD=1 \
    all
```

## Installed layout (per recipe)

- Commands: `/usr/bin/sslh`, `/usr/bin/sslh-ev`, `/usr/bin/sslh-fork`, `/usr/bin/sslh-select`
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[sslh]] · [[Kali Gap Analysis]]
