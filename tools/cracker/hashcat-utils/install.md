---
tool: "hashcat-utils"
build-system: "make"
upstream: "https://github.com/hashcat/hashcat-utils"
recipe: "arch-official/extra"
nix-package: "hashcat-utils"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/hashcat-utils/-/raw/main/PKGBUILD"
version: "1.10"
license: "unknown"
---

# Installing hashcat-utils

> Set of small utilities for advanced password cracking

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/hashcat-utils/-/raw/main/PKGBUILD) (version `1.10`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S hashcat-utils
```

## Kali / Debian-based — same package name

```bash
sudo apt install hashcat-utils
```

## Nix (works on any distro)

```bash
nix run nixpkgs#hashcat-utils    # ad-hoc run, no install
nix profile install nixpkgs#hashcat-utils   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/hashcat/hashcat-utils.git
cd hashcat-utils
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  make -C src
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[hashcat-utils]] · [[Kali Gap Analysis]]
