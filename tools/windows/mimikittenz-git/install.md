---
tool: "mimikittenz-git"
ebuild-package: "mimikittenz-git"
build-system: "scripts"
upstream: "https://github.com/orlyjamie/mimikittenz"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/mimikittenz-git"
version: "20160709.r8"
license: "custom"
---

# Installing mimikittenz-git

> A post-exploitation powershell tool for extracting juicy info from memory

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/mimikittenz-git) (version `20160709.r8`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S mimikittenz-git
```

The same PKGBUILD is usually on the AUR (`paru -S mimikittenz-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/orlyjamie/mimikittenz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[windows]] · [[mimikittenz-git]] · [[ArchStrike Gap Analysis]]
