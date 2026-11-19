---
tool: "auto-xor-decryptographyr-git"
ebuild-package: "auto-xor-decryptographyr-git"
build-system: "scripts"
upstream: "http://www.blog.mrg-effitas.com/publishing-of-mrg-effitas-automatic-xor-decryptographyr-tool/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/auto-xor-decryptographyr-git"
version: "7.2eb176d"
license: "GPL"
---

# Installing auto-xor-decryptographyr-git

> Automatic XOR decryptographyr tool.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/auto-xor-decryptographyr-git) (version `7.2eb176d`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S auto-xor-decryptographyr-git
```

The same PKGBUILD is usually on the AUR (`paru -S auto-xor-decryptographyr-git`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/MRGEffitas/scripts
cd scripts
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `argparse`, `future`, `more-itertools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[crypto]] · [[auto-xor-decryptographyr-git]] · [[ArchStrike Gap Analysis]]
