---
tool: "blue-hydra"
build-system: "scripts"
upstream: "https://github.com/pwnieexpress/blue_hydra"
recipe: "blackarch"
source: "https://github.com/pwnieexpress/blue_hydra"
license: "BSD"
blackarch-pkgver: "710.1c2372d"
kali-package: "blue-hydra"
kali-version: "1.9.21-0kali1"
---

# Installing blue-hydra

> A Bluetooth device discovery service built on top of the bluez library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blue-hydra) (version `710.1c2372d`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S blue-hydra    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install blue-hydra    # Kali package version 1.9.21-0kali1
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/pwnieexpress/blue_hydra.git
cd blue_hydra
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libxslt` — Debian libxslt1-dev / Fedora libxslt-devel
  - `ruby`
  - `ruby-bundler`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[blue-hydra]] · [[BlackArch Tools]]

