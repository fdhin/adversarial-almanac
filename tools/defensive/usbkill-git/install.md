---
tool: "usbkill-git"
ebuild-package: "usbkill-git"
build-system: "scripts"
upstream: "https://github.com/hephaest0s/usbkill"
recipe: "archstrike-pkgbuild"
aur-package: "usbkill-git"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/usbkill-git"
version: "r213.d3df79e"
license: "custom"
---

# Installing usbkill-git

> An anti-forensic kill-switch that waits for a change on your USB ports and then immediately shuts down your computer.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/usbkill-git) (version `r213.d3df79e`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S usbkill-git
```

Also on the AUR: `paru -S usbkill-git` (version `r213.d3df79e-1`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/hephaest0s/usbkill.git
cd usbkill
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[defensive]] · [[usbkill-git]] · [[ArchStrike Gap Analysis]]
