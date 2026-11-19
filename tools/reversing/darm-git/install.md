---
tool: "darm-git"
ebuild-package: "darm-git"
build-system: "make"
upstream: "https://github.com/jbremer/darm"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/darm-git"
version: "416.cadffa7"
license: "custom"
---

# Installing darm-git

> An ARMv7 disassembling library written in C.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/darm-git) (version `416.cadffa7`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S darm-git
```

The same PKGBUILD is usually on the AUR (`paru -S darm-git`).

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone git://github.com/jbremer/darm.git
cd darm
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `python`

## Exact build steps from the recipe

```bash
cd "${srcdir}/darm" || exit
  make -j1
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[reversing]] · [[darm-git]] · [[ArchStrike Gap Analysis]]
