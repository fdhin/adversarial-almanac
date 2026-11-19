---
tool: "depdep"
build-system: "scripts"
upstream: "https://github.com/galkan/depdep"
recipe: "blackarch"
source: "https://raw.githubusercontent.com/galkan/depdep/master/LICENSE.txt"
license: "custom:unknown"
blackarch-pkgver: "2.0"
---

# Installing depdep

> A merciless sentinel which will seek sensitive files containing critical info leaking through your network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/depdep) (version `2.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S depdep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/depdep) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `apache-tika`
  - `cifs-utils`
  - `python2`
  - `smbclient`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[depdep]] · [[BlackArch Tools]]

