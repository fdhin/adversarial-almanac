---
tool: "binwalk"
build-system: "go"
upstream: "https://github.com/ReFirmLabs/binwalk"
recipe: "arch-official/extra"
nix-package: "binwalk"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/binwalk/-/raw/main/PKGBUILD"
version: "3.1.0"
license: "unknown"
---

# Installing binwalk

> tool library for analyzing binary blobs and executable code

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/binwalk/-/raw/main/PKGBUILD) (version `3.1.0`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S binwalk
```

## Kali / Debian-based — same package name

```bash
sudo apt install binwalk
```

## Nix (works on any distro)

```bash
nix run nixpkgs#binwalk    # ad-hoc run, no install
nix profile install nixpkgs#binwalk   # or persist it
```

## Any other Linux distro

**A. Prebuilt binary (easiest):** check [upstream releases](https://github.com/ReFirmLabs/binwalk/releases) for a static binary.

**B. Build from source (needs `go`):**

```bash
git clone https://github.com/ReFirmLabs/binwalk.git
cd binwalk
go build ./...   # exact recipe flags below
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  cargo build --release --locked
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[binwalk]] · [[Kali Gap Analysis]]
