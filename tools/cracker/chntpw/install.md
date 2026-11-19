---
tool: "chntpw"
build-system: "make"
upstream: "https://pogostick.net/~pnh/ntpasswd/"
recipe: "arch-official/extra"
nix-package: "chntpw"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/chntpw/-/raw/main/PKGBUILD"
version: "140201"
license: "GPL, LGPL"
---

# Installing chntpw

> NT SAM password recovery utility

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/chntpw/-/raw/main/PKGBUILD) (version `140201`, license GPL, LGPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S chntpw
```

## Kali / Debian-based — same package name

```bash
sudo apt install chntpw
```

## Nix (works on any distro)

```bash
nix run nixpkgs#chntpw    # ad-hoc run, no install
nix profile install nixpkgs#chntpw   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://pogostick.net/~pnh/ntpasswd/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"
  CFLAGS+=" -Wno-incompatible-pointer-types"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[chntpw]] · [[Kali Gap Analysis]]
