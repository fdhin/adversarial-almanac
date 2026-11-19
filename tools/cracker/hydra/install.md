---
tool: "hydra"
build-system: "autotools"
upstream: "https://github.com/vanhauser-thc/thc-hydra"
recipe: "arch-official/extra"
nix-package: "hydra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/hydra/-/raw/main/PKGBUILD"
version: "9.7"
license: "AGPL-3.0-or-later"
---

# Installing hydra

> very fast network logon cracker

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/hydra/-/raw/main/PKGBUILD) (version `9.7`, license AGPL-3.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S hydra
```

## Kali / Debian-based — same package name

```bash
sudo apt install hydra
```

## Nix (works on any distro)

```bash
nix run nixpkgs#hydra    # ad-hoc run, no install
nix profile install nixpkgs#hydra   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/vanhauser-thc/thc-hydra.git
cd thc-hydra
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd thc-hydra-${pkgver}
  ./configure --fhs
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[hydra]] · [[Kali Gap Analysis]]
