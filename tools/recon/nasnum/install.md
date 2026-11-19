---
tool: "nasnum"
build-system: "scripts"
upstream: "https://github.com/tcstool/nasnum.git"
recipe: "blackarch"
source: "https://github.com/tcstool/nasnum.git"
license: "GPL3"
blackarch-pkgver: "5.df5df19"
---

# Installing nasnum

> Script to enumerate network attached storages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nasnum) (version `5.df5df19`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nasnum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tcstool/nasnum.git) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `ncftp`
  - `net-snmp`
  - `nfs-utils`
  - `smbclient`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[nasnum]] · [[BlackArch Tools]]

