---
tool: "apnbf"
build-system: "scripts"
upstream: "http://www.c0decafe.de/"
recipe: "blackarch"
source: "http://www.c0decafe.de/"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing apnbf

> A small python script designed for enumerating valid APNs (Access Point Name) on a GTP-C speaking device.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apnbf) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S apnbf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.c0decafe.de/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[apnbf]] · [[BlackArch Tools]]

