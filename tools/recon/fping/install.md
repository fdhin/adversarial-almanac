---
tool: "fping"
build-system: "autotools"
upstream: "https://www.fping.org/"
recipe: "arch-official/extra"
nix-package: "fping"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/fping/-/raw/main/PKGBUILD"
version: "5.5"
license: "custom"
---

# Installing fping

> sends ICMP ECHO_REQUEST packets to network hosts

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/fping/-/raw/main/PKGBUILD) (version `5.5`, license custom).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S fping
```

## Kali / Debian-based — same package name

```bash
sudo apt install fping
```

## Nix (works on any distro)

```bash
nix run nixpkgs#fping    # ad-hoc run, no install
nix profile install nixpkgs#fping   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.fping.org/) (the recipe uses a versioned archive).

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
cd ${pkgname}-${pkgver}
  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --mandir=/usr/share/man \
    --enable-ipv4 \
    --enable-ipv6
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[fping]] · [[Kali Gap Analysis]]
