---
tool: "goofile"
ebuild-package: "goofile"
build-system: "scripts"
upstream: "https://code.google.com/p/goofile/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/goofile"
version: "1.5"
license: "MIT"
---

# Installing goofile

> Command line filetype search

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/goofile) (version `1.5`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S goofile
```

The same PKGBUILD is usually on the AUR (`paru -S goofile`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/goofile/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[goofile]] · [[ArchStrike Gap Analysis]]
