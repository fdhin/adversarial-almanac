---
tool: "ntp-ip-enum"
build-system: "scripts"
upstream: "http://www.securepla.net/"
recipe: "blackarch"
source: "http://www.securepla.net/"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing ntp-ip-enum

> Script to pull addresses from a NTP server using the monlist command. Can also output Maltego resultset.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntp-ip-enum) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ntp-ip-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.securepla.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ntp-ip-enum]] · [[BlackArch Tools]]

