---
tool: "bamfdetect"
ebuild-package: "bamfdetect"
build-system: "scripts"
upstream: "https://github.com/bwall/bamfdetect"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/bamfdetect"
version: "1.6.12"
license: "MIT"
---

# Installing bamfdetect

> Identifies and extracts information from bots and other malware

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/bamfdetect) (version `1.6.12`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S bamfdetect
```

The same PKGBUILD is usually on the AUR (`paru -S bamfdetect`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bwall/bamfdetect) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- ⚠️ Python 2 only deps: python2-crypto, python2-pbkdf2, python2-pefile, python2-rarfile, python2-setuptools, python2-yara (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[bamfdetect]] · [[ArchStrike Gap Analysis]]
