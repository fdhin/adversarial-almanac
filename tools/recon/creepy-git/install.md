---
tool: "creepy-git"
ebuild-package: "creepy-git"
build-system: "scripts"
upstream: "http://github.com/ilektrojohn/creepy"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/creepy-git"
version: "r137.9f60449"
license: "GPL3"
---

# Installing creepy-git

> A geolocation information gatherer. Offers geolocation information gathering through social networking platforms.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/creepy-git) (version `r137.9f60449`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S creepy-git
```

The same PKGBUILD is usually on the AUR (`paru -S creepy-git`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/ilektrojohn/creepy.git
cd creepy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-configobj, python2-flickrapi, python2-instagram, python2-pyqt4, python2-tweepy, python2-yapsy (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/creepy`

Backlinks: [[recon]] · [[creepy-git]] · [[ArchStrike Gap Analysis]]
