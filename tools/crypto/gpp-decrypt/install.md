---
tool: "gpp-decrypt"
build-system: "scripts"
upstream: "https://github.com/t0thkr1s/gpp-decrypt"
recipe: "blackarch"
source: "https://github.com/t0thkr1s/gpp-decrypt"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v1.1.r4.g86ae8b7"
kali-package: "gpp-decrypt"
kali-version: "0.1-1kali3"
---

# Installing gpp-decrypt

> Parse the Group Policy Preferences XML file which extracts the username and decrypts the cpassword attribute.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gpp-decrypt) (version `v1.1.r4.g86ae8b7`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S gpp-decrypt    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install gpp-decrypt    # Kali package version 0.1-1kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/t0thkr1s/gpp-decrypt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `crypto`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[gpp-decrypt]] · [[BlackArch Tools]]

