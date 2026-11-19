---
tool: "salsapipe-git"
ebuild-package: "salsapipe-git"
build-system: "make"
upstream: "https://github.com/prurigro/salsapipe"
recipe: "archstrike-pkgbuild"
aur-package: "salsapipe-git"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/salsapipe-git"
version: "20150727.r26.6d81ace"
license: "GPL3"
---

# Installing salsapipe-git

> Encrypted network tunneling using salsa20 from libnettle and GPG from libgpgme

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/salsapipe-git) (version `20150727.r26.6d81ace`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S salsapipe-git
```

Also on the AUR: `paru -S salsapipe-git` (version `20150727.r26.6d81ace-4`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/prurigro/salsapipe) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gpgme`

## Exact build steps from the recipe

```bash
cd $_pkgname
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/salsamsg`

Backlinks: [[tunnel]] · [[salsapipe-git]] · [[ArchStrike Gap Analysis]]
