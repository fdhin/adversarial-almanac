---
tool: "callerpy-git"
ebuild-package: "callerpy-git"
build-system: "scripts"
upstream: "https://github.com/Logic-gate/callerpy"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/callerpy-git"
version: "20140518.r40"
license: "GPL2"
---

# Installing callerpy-git

> Truecaller Name Retriever

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/callerpy-git) (version `20140518.r40`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S callerpy-git
```

The same PKGBUILD is usually on the AUR (`paru -S callerpy-git`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/Logic-gate/callerpy.git
cd callerpy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-mechanize (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[callerpy-git]] · [[ArchStrike Gap Analysis]]
