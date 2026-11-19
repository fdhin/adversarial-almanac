---
tool: "bt2-git"
ebuild-package: "bt2-git"
build-system: "scripts"
upstream: "https://github.com/blazeinfosec/bt2"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/bt2-git"
version: "20160527.r18"
license: "Apache"
---

# Installing bt2-git

> A post-exploitation tool that leverages the infrastructure of Telegram as a C&C

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/bt2-git) (version `20160527.r18`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S bt2-git
```

The same PKGBUILD is usually on the AUR (`paru -S bt2-git`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/blazeinfosec/bt2.git
cd bt2
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-telepot (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[bluetooth]] · [[bt2-git]] · [[ArchStrike Gap Analysis]]
