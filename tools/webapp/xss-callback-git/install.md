---
tool: "xss-callback-git"
ebuild-package: "xss-callback-git"
build-system: "node"
upstream: "https://github.com/PaulSec/XSS-Callback"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/xss-callback-git"
version: "r14.6a2bb85"
license: "MIT"
---

# Installing xss-callback-git

> A lightweight HTTP Server that exploits XSS victim's session automatically

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/xss-callback-git) (version `r14.6a2bb85`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S xss-callback-git
```

The same PKGBUILD is usually on the AUR (`paru -S xss-callback-git`).

## Any other Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/PaulSec/XSS-Callback.git
cd XSS-Callback
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[xss-callback-git]] · [[ArchStrike Gap Analysis]]
