---
tool: "cjdnsify-git"
ebuild-package: "cjdnsify-git"
build-system: "scripts"
upstream: "https://github.com/prurigro/cjdnsify"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cjdnsify-git"
version: "20140717.r6.bc354af"
license: "GPL"
---

# Installing cjdnsify-git

> Limit network access of bind-compatible programs to the local cjdns network

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/cjdnsify-git) (version `20140717.r6.bc354af`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S cjdnsify-git
```

The same PKGBUILD is usually on the AUR (`paru -S cjdnsify-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/prurigro/cjdnsify) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `cjdns`
  - `force_bind`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[cjdnsify-git]] · [[ArchStrike Gap Analysis]]
