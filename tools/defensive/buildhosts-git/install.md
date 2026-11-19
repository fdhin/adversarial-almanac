---
tool: "buildhosts-git"
ebuild-package: "buildhosts-git"
build-system: "scripts"
upstream: "https://github.com/prurigro/buildhosts"
recipe: "archstrike-pkgbuild"
aur-package: "buildhosts-git"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/buildhosts-git"
version: "20170524.r12.99ed481"
license: "MIT"
---

# Installing buildhosts-git

> Download and use custom hosts sources to build /etc/hosts

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/buildhosts-git) (version `20170524.r12.99ed481`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S buildhosts-git
```

Also on the AUR: `paru -S buildhosts-git` (version `20241104.r13.1f7ef74-1`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/prurigro/buildhosts) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[buildhosts-git]] · [[ArchStrike Gap Analysis]]
