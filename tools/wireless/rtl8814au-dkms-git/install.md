---
tool: "rtl8814au-dkms-git"
build-system: "scripts"
upstream: "https://github.com/morrownr/8814au"
recipe: "blackarch"
aur-package: "rtl8814au-dkms-git"
source: "https://github.com/morrownr/8814au"
license: "GPL2"
blackarch-pkgver: "5.8.5.1.r207.g1840d7b"
---

# Installing rtl8814au-dkms-git

> RTL8814AU and RTL8813AU chipset driver with firmware v5.8.5.1.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rtl8814au-dkms-git) (version `5.8.5.1.r207.g1840d7b`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S rtl8814au-dkms-git    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/morrownr/8814au) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bc`
  - `dkms`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[rtl8814au-dkms-git]] · [[BlackArch Tools]]

