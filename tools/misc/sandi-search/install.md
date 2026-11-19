---
tool: "sandi-search"
ebuild-package: "sandi-search"
build-system: "scripts"
upstream: "https://sourceforge.net/projects/sandisearch/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sandi-search"
version: "13"
license: "GPL"
---

# Installing sandi-search

> An open source project for search the exploits from exploit databases ...

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/sandi-search) (version `13`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S sandi-search
```

The same PKGBUILD is usually on the AUR (`paru -S sandi-search`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/sandisearch/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/sandi`, `/usr/bin/sandi-gui`

Backlinks: [[misc]] · [[sandi-search]] · [[ArchStrike Gap Analysis]]
