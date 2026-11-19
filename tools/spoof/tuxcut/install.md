---
tool: "tuxcut"
ebuild-package: "tuxcut"
build-system: "scripts"
upstream: "https://github.com/a-atalla/tuxcut/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/tuxcut"
version: "6.1"
license: "GPL2"
---

# Installing tuxcut

> Netcut-like program for Linux written in PyQt

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/tuxcut) (version `6.1`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S tuxcut
```

The same PKGBUILD is usually on the AUR (`paru -S tuxcut`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/a-atalla/tuxcut/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `altgraph`, `appdirs`, `apscheduler`, `bottle`, `future`, `macholib`, `netifaces`, `packaging`, `pefile`, `pylint`, `pyparsing`, `pytz`, `requests`, `setproctitle`, `six`, `tzlocal`, `wxpython`
- **System libraries** (Arch name — translate for your distro):
  - `arp-scan`
  - `arptables`
  - `dsniff`
  - `iproute2`
  - `kamene`
  - `pyinstaller`

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[spoof]] · [[tuxcut]] · [[ArchStrike Gap Analysis]]
