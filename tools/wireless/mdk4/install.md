---
tool: "mdk4"
build-system: "make"
upstream: "https://github.com/aircrack-ng/mdk4"
recipe: "arch-official/extra"
nix-package: "mdk4"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/mdk4/-/raw/main/PKGBUILD"
version: "4.2"
license: "unknown"
---

# Installing mdk4

> Wireless attack tool for IEEE 802.11 networks

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/mdk4/-/raw/main/PKGBUILD) (version `4.2`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S mdk4
```

## Kali / Debian-based — same package name

```bash
sudo apt install mdk4
```

## Nix (works on any distro)

```bash
nix run nixpkgs#mdk4    # ad-hoc run, no install
nix profile install nixpkgs#mdk4   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/aircrack-ng/mdk4.git
cd mdk4
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/man`

Backlinks: [[wireless]] · [[mdk4]] · [[Kali Gap Analysis]]
