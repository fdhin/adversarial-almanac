---
tool: "fern-wifi-cracker"
build-system: "scripts"
upstream: "http://code.google.com/p/fern-wifi-cracker/"
recipe: "blackarch"
source: "http://code.google.com/p/fern-wifi-cracker/"
license: "GPL2"
blackarch-pkgver: "303.58ed54e"
kali-package: "fern-wifi-cracker"
kali-version: "3.6-0kali1"
---

# Installing fern-wifi-cracker

> WEP, WPA wifi cracker for wireless penetration testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fern-wifi-cracker) (version `303.58ed54e`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S fern-wifi-cracker    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install fern-wifi-cracker    # Kali package version 3.6-0kali1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/fern-wifi-cracker/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pyqt5`, `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `macchanger`
  - `python`
  - `subversion`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[fern-wifi-cracker]] · [[BlackArch Tools]]

