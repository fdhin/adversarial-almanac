---
tool: "smbspider"
build-system: "scripts"
upstream: "https://github.com/T-S-A/smbspider"
recipe: "blackarch"
source: "https://github.com/T-S-A/smbspider"
license: "GPL2"
blackarch-pkgver: "10.7db9323"
---

# Installing smbspider

> A lightweight python utility for searching SMB/CIFS/Samba file shares.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbspider) (version `10.7db9323`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S smbspider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/T-S-A/smbspider) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-netaddr, python2-pysmb, python2-pysmbc (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smbspider]] · [[BlackArch Tools]]

