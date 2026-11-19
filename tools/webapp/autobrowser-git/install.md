---
tool: "autobrowser-git"
ebuild-package: "autobrowser-git"
build-system: "scripts"
upstream: "https://github.com/El3ct71k/AutoBrowser"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/autobrowser-git"
version: "20161027.r24"
license: "GPL"
---

# Installing autobrowser-git

> A python analysing and reporting tool for pentesters

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/autobrowser-git) (version `20161027.r24`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S autobrowser-git
```

The same PKGBUILD is usually on the AUR (`paru -S autobrowser-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/El3ct71k/AutoBrowser) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ghost.py`
  - `python2`
- ⚠️ Python 2 only deps: python2-colorlog, python2-nmap, python2-pyqt4 (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/autobrowser`

Backlinks: [[webapp]] · [[autobrowser-git]] · [[ArchStrike Gap Analysis]]
