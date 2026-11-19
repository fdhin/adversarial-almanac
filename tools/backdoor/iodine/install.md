---
tool: "iodine"
build-system: "other"
upstream: "https://code.kryo.se/iodine"
recipe: "arch-official/extra"
nix-package: "iodine"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/iodine/-/raw/main/PKGBUILD"
version: "0.9.0"
license: "ISC, Zlib"
---

# Installing iodine

> tool for tunneling IPv4 data through a DNS server

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/iodine/-/raw/main/PKGBUILD) (version `0.9.0`, license ISC, Zlib).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S iodine
```

## Kali / Debian-based — same package name

```bash
sudo apt install iodine
```

## Nix (works on any distro)

```bash
nix run nixpkgs#iodine    # ad-hoc run, no install
nix profile install nixpkgs#iodine   # or persist it
```

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/yarrick/iodine.git
cd iodine
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libcap-ng`
  - `meson`
  - `systemd-libs`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
arch-meson $pkgname build --sbindir=bin
  meson compile -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[backdoor]] · [[iodine]] · [[Kali Gap Analysis]]
