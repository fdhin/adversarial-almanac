---
tool: "csrft-git"
ebuild-package: "csrft-git"
build-system: "node"
upstream: "https://github.com/PaulSec/CSRFT"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/csrft-git"
version: "r32.17146bb"
license: "GPL3"
---

# Installing csrft-git

> In few words, this is a simple HTTP Server in NodeJS that will communicate with the clients (victims) and send them payload that will be executed using JavaScript

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/csrft-git) (version `r32.17146bb`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S csrft-git
```

The same PKGBUILD is usually on the AUR (`paru -S csrft-git`).

## Any other Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/PaulSec/CSRFT.git
cd CSRFT
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[csrft-git]] · [[ArchStrike Gap Analysis]]
