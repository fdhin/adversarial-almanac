---
tool: "meltdown-git"
ebuild-package: "meltdown-git"
build-system: "make"
upstream: "https://github.com/IAIK/meltdown"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/meltdown-git"
version: "20191026.r84"
license: "custom"
---

# Installing meltdown-git

> Meltdown Proof-of-Concept

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/meltdown-git) (version `20191026.r84`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S meltdown-git
```

The same PKGBUILD is usually on the AUR (`paru -S meltdown-git`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/IAIK/meltdown) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}" || exit
  make

  cd "${srcdir}/${pkgname}/libkdump" || exit
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[scanner]] · [[meltdown-git]] · [[ArchStrike Gap Analysis]]
