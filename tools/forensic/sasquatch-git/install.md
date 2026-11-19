---
tool: "sasquatch-git"
ebuild-package: "sasquatch-git"
build-system: "scripts"
upstream: "https://github.com/devttys0/sasquatch"
recipe: "archstrike-pkgbuild"
aur-package: "sasquatch-git"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sasquatch-git"
version: "20210324.r60"
license: "GPL"
---

# Installing sasquatch-git

> Tools for squashfs, a highly compressed read-only filesystem for Linux.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sasquatch-git) (version `20210324.r60`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S sasquatch-git
```

Also on the AUR: `paru -S sasquatch-git` (version `r60.bd864a1-1`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/devttys0/sasquatch) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `gzip`
  - `lz4`
  - `lzo`
  - `xz`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[sasquatch-git]] · [[ArchStrike Gap Analysis]]
