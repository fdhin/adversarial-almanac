---
tool: "delta-git"
ebuild-package: "delta-git"
build-system: "scripts"
upstream: "https://github.com/pangbo-1988/data_diff"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/delta-git"
version: "20160608.r5"
license: "GPL3"
---

# Installing delta-git

> Track the changes of a website

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/delta-git) (version `20160608.r5`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S delta-git
```

The same PKGBUILD is usually on the AUR (`paru -S delta-git`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/pangbo-1988/data_diff
cd data_diff
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `elasticsearch`
  - `python2`
  - `scrapy`
- ⚠️ Python 2 only deps: python2-elasticsearch-py, python2-mmh3, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[delta-git]] · [[ArchStrike Gap Analysis]]
