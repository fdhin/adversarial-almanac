---
tool: "tchunt-ng"
build-system: "scripts"
upstream: "https://github.com/antagon/TCHunt-ng"
recipe: "blackarch"
source: "https://github.com/antagon/TCHunt-ng"
license: "GPL3"
blackarch-pkgver: "208.b8cf7fc"
---

# Installing tchunt-ng

> Reveal encrypted files stored on a filesystem.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tchunt-ng) (version `208.b8cf7fc`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tchunt-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/antagon/TCHunt-ng.git
cd TCHunt-ng
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[tchunt-ng]] · [[BlackArch Tools]]

