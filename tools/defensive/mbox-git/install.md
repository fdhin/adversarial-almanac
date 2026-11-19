---
tool: "mbox-git"
ebuild-package: "mbox-git"
build-system: "autotools"
upstream: "http://pdos.csail.mit.edu/${_pkgname}"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/mbox-git"
version: "20140526.r274.a131424"
license: "MIT"
---

# Installing mbox-git

> A lightweight sandbox tool for non-root users

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/mbox-git) (version `20140526.r274.a131424`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S mbox-git
```

The same PKGBUILD is usually on the AUR (`paru -S mbox-git`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://pdos.csail.mit.edu/${_pkgname}) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}/src
    cp {.,}configsbox.h
    ./configure --prefix=/usr
    make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[mbox-git]] · [[ArchStrike Gap Analysis]]
