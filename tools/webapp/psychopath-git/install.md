---
tool: "psychopath-git"
ebuild-package: "psychopath-git"
build-system: "scripts"
upstream: "https://github.com/ewilded/psychoPATH"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/psychopath-git"
version: "20180724.r251"
license: "GPL"
---

# Installing psychopath-git

> an advanced path traversal tool

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/psychopath-git) (version `20180724.r251`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S psychopath-git
```

The same PKGBUILD is usually on the AUR (`paru -S psychopath-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ewilded/psychoPATH) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `java-openjfx`
  - `java-runtime`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[psychopath-git]] · [[ArchStrike Gap Analysis]]
