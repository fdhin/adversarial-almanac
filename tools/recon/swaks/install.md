---
tool: "swaks"
build-system: "perl"
upstream: "https://jetmore.org/john/code/${pkgname}/"
recipe: "arch-official/extra"
nix-package: "swaks"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/swaks/-/raw/main/PKGBUILD"
version: "20240103.0"
license: "GPL-2.0-or-later"
---

# Installing swaks

> SMTP command-line test tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/swaks/-/raw/main/PKGBUILD) (version `20240103.0`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S swaks
```

## Kali / Debian-based — same package name

```bash
sudo apt install swaks
```

## Nix (works on any distro)

```bash
nix run nixpkgs#swaks    # ad-hoc run, no install
nix profile install nixpkgs#swaks   # or persist it
```

## Any other Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://www.jetmore.org/john/code/swaks/) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `perl`
  - `perl-net-dns`
  - `perl-net-ssleay`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/man`

Backlinks: [[recon]] · [[swaks]] · [[Kali Gap Analysis]]
