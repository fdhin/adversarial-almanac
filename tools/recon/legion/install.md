---
tool: "legion"
build-system: "scripts"
upstream: "https://github.com/carlospolop/legion"
recipe: "blackarch"
source: "https://github.com/carlospolop/legion"
license: "MIT"
blackarch-pkgver: "64.703f2ad"
kali-package: "legion"
kali-version: "0.7.0-0kali2"
---

# Installing legion

> Automatic Enumeration Tool based in Open Source tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/legion) (version `64.703f2ad`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S legion    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install legion    # Kali package version 0.7.0-0kali2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/carlospolop/legion) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `arjun`
  - `dirhunt`
  - `dirsearch`
  - `impacket`
  - `python`
  - `rpcbind`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[legion]] · [[BlackArch Tools]]

