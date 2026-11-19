---
tool: "sstat-git"
ebuild-package: "sstat-git"
build-system: "scripts"
upstream: "https://bitbucket.org/R4v3N/sstat.sh"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sstat-git"
version: "20141023.r8"
license: "GPL"
---

# Installing sstat-git

> Check the status of a server while fuzzing or DDoSing

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sstat-git) (version `20141023.r8`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S sstat-git
```

The same PKGBUILD is usually on the AUR (`paru -S sstat-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://bitbucket.org/R4v3N/sstat.sh) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bind-tools`
  - `curl`
  - `mpv`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[misc]] · [[sstat-git]] · [[ArchStrike Gap Analysis]]
