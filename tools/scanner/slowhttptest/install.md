---
tool: "slowhttptest"
build-system: "autotools"
upstream: "https://github.com/shekyan/slowhttptest"
recipe: "arch-official/extra"
nix-package: "slowhttptest"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/slowhttptest/-/raw/main/PKGBUILD"
version: "1.10.0"
license: "Apache"
---

# Installing slowhttptest

> application layer Denial of Service attacks simulation tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/slowhttptest/-/raw/main/PKGBUILD) (version `1.10.0`, license Apache).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S slowhttptest
```

## Kali / Debian-based — same package name

```bash
sudo apt install slowhttptest
```

## Nix (works on any distro)

```bash
nix run nixpkgs#slowhttptest    # ad-hoc run, no install
nix profile install nixpkgs#slowhttptest   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/shekyan/slowhttptest.git
cd slowhttptest
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libgcc`
  - `libstdc++`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[scanner]] · [[slowhttptest]] · [[Kali Gap Analysis]]
