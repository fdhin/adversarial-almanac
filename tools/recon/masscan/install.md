---
tool: "masscan"
build-system: "make"
upstream: "https://github.com/robertdavidgraham/masscan"
recipe: "arch-official/extra"
nix-package: "masscan"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/masscan/-/raw/main/PKGBUILD"
version: "1.3.2"
license: "AGPL3"
---

# Installing masscan

> TCP port scanner

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/masscan/-/raw/main/PKGBUILD) (version `1.3.2`, license AGPL3).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S masscan
```

## Kali / Debian-based — same package name

```bash
sudo apt install masscan
```

## Nix (works on any distro)

```bash
nix run nixpkgs#masscan    # ad-hoc run, no install
nix profile install nixpkgs#masscan   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/robertdavidgraham/masscan.git
cd masscan
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  export CFLAGS+=" ${CPPFLAGS}"
  make CC=gcc
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[masscan]] · [[Kali Gap Analysis]]
