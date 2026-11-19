---
tool: "sleuthkit"
build-system: "perl"
upstream: "https://www.sleuthkit.org/sleuthkit"
recipe: "arch-official/extra"
nix-package: "sleuthkit"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/sleuthkit/-/raw/main/PKGBUILD"
version: "4.15.0"
license: "CPL-1.0, GPL-2.0-or-later, IPL-1.0"
---

# Installing sleuthkit

> tools for forensics analysis on volume and filesystem data

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/sleuthkit/-/raw/main/PKGBUILD) (version `4.15.0`, license CPL-1.0, GPL-2.0-or-later, IPL-1.0).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S sleuthkit
```

## Kali / Debian-based — same package name

```bash
sudo apt install sleuthkit
```

## Nix (works on any distro)

```bash
nix run nixpkgs#sleuthkit    # ad-hoc run, no install
nix profile install nixpkgs#sleuthkit   # or persist it
```

## Any other Linux distro

**From source (needs `perl`):**

**Source:** download the release from the [upstream site](https://www.sleuthkit.org/sleuthkit) (the recipe uses a versioned archive).

```bash
sudo cpan <missing modules>   # recipe dependency list below
perl <tool>.pl
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `autoconf-archive`
  - `glibc`
  - `libewf`
  - `libgcc`
  - `libstdc++`
  - `perl`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[sleuthkit]] · [[Kali Gap Analysis]]
