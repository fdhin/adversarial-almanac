---
tool: "blueranger"
build-system: "scripts"
upstream: "https://www.hackfromacave.com/projects/blueranger.html"
recipe: "blackarch"
source: "https://www.hackfromacave.com/projects/blueranger.html"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.1"
kali-package: "blueranger"
kali-version: "0.1-1kali7"
---

# Installing blueranger

> A simple Bash script which uses Link Quality to locate Bluetooth device radios.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blueranger) (version `1.1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S blueranger    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install blueranger    # Kali package version 0.1-1kali7
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.hackfromacave.com/projects/blueranger.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[blueranger]] · [[BlackArch Tools]]

