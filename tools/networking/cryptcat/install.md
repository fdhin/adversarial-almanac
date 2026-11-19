---
tool: "cryptcat"
ebuild-package: "cryptcat"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/cryptcat"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cryptcat"
version: "1.2.1"
license: "GPL"
---

# Installing cryptcat

> A lightweight version of netcat with integrated transport encryption capabilities.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cryptcat) (version `1.2.1`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S cryptcat
```

The same PKGBUILD is usually on the AUR (`paru -S cryptcat`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/cryptcat) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[networking]] · [[cryptcat]] · [[ArchStrike Gap Analysis]]
