---
tool: "hxd"
build-system: "scripts"
upstream: "https://mh-nexus.de/en/hxd/"
recipe: "blackarch"
aur-package: "hxd"
source: "https://mh-nexus.de/downloads/HxDSetup.zip"
license: "custom:unknown"
blackarch-pkgver: "2.5.0.0"
---

# Installing hxd

> Freeware Hex Editor and Disk Editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hxd) (version `2.5.0.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hxd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://mh-nexus.de/downloads/HxDSetup.zip
tar xf HxDSetup.zip
cd HxDSetup
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `innoextract`
  - `wine`
  - `wine-mono`
  - `wine_gecko`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[misc]] · [[hxd]] · [[BlackArch Tools]]

