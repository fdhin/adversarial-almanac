---
tool: "sandmap"
build-system: "scripts"
upstream: "https://github.com/trimstray/sandmap"
recipe: "blackarch"
source: "https://github.com/trimstray/sandmap"
license: "GPL3"
blackarch-pkgver: "581.5be99d4"
---

# Installing sandmap

> Simple CLI with the ability to run pure Nmap engine, 31 modules with 459 scan profiles.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sandmap) (version `581.5be99d4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sandmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/trimstray/sandmap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `proxychains-ng`
  - `tor`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[sandmap]] · [[BlackArch Tools]]

