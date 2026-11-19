---
tool: "geoedge"
build-system: "scripts"
upstream: ""
recipe: "blackarch"
source: ""
license: "GPL"
blackarch-pkgver: "0.2"
---

# Installing geoedge

> This little tools is designed to get geolocalization information of a host, it get the information from two sources (maxmind and geoiptool).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/geoedge) (version `0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S geoedge    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[geoedge]] · [[BlackArch Tools]]

