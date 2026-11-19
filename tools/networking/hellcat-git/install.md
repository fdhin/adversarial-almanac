---
tool: "hellcat-git"
ebuild-package: "hellcat-git"
build-system: "make"
upstream: "https://github.com/matildah/hellcat"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/hellcat-git"
version: "r45.8873f05"
license: "MIT"
---

# Installing hellcat-git

> netcat that takes unfair advantage of traffic shaping systems that don't initially ratelimit

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/hellcat-git) (version `r45.8873f05`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S hellcat-git
```

The same PKGBUILD is usually on the AUR (`paru -S hellcat-git`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/matildah/hellcat) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `glibc`

## Exact build steps from the recipe

```bash
cd $_pkgname
    make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[networking]] · [[hellcat-git]] · [[ArchStrike Gap Analysis]]
