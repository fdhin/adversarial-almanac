---
tool: "gerix-wifi-cracker"
build-system: "scripts"
upstream: "https://github.com/TigerSecurity"
recipe: "blackarch"
source: "https://github.com/TigerSecurity"
license: "GPL"
blackarch-pkgver: "1.1c3cd73"
---

# Installing gerix-wifi-cracker

> A graphical user interface for aircrack-ng and pyrit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gerix-wifi-cracker) (version `1.1c3cd73`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S gerix-wifi-cracker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/TigerSecurity) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `pyqt3`
  - `python2`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[gerix-wifi-cracker]] · [[BlackArch Tools]]

