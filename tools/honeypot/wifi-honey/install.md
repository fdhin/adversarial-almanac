---
tool: "wifi-honey"
build-system: "scripts"
upstream: "http://www.digininja.org/projects/wifi_honey.php"
recipe: "blackarch"
source: "http://www.digininja.org/projects/wifi_honey.php"
license: "CCPL"
blackarch-pkgver: "1.0"
kali-package: "wifi-honey"
kali-version: "1.0-1kali3"
---

# Installing wifi-honey

> A management tool for wifi honeypots.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifi-honey) (version `1.0`, license CCPL).

## Arch / BlackArch

```bash
sudo pacman -S wifi-honey    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install wifi-honey    # Kali package version 1.0-1kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.digininja.org/projects/wifi_honey.php) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `screen`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[wifi-honey]] · [[BlackArch Tools]]

