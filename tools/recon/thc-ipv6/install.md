---
tool: "thc-ipv6"
build-system: "make"
upstream: "https://github.com/vanhauser-thc/thc-ipv6"
recipe: "arch-official/extra"
nix-package: "thc-ipv6"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/thc-ipv6/-/raw/main/PKGBUILD"
version: "3.8"
license: "unknown"
---

# Installing thc-ipv6

> The Hacker Choice's IPv6 Attack Toolkit

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/thc-ipv6/-/raw/main/PKGBUILD) (version `3.8`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S thc-ipv6
```

## Kali / Debian-based — same package name

```bash
sudo apt install thc-ipv6
```

## Nix (works on any distro)

```bash
nix run nixpkgs#thc-ipv6    # ad-hoc run, no install
nix profile install nixpkgs#thc-ipv6   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/vanhauser-thc/thc-ipv6.git
cd thc-ipv6
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[thc-ipv6]] · [[Kali Gap Analysis]]
