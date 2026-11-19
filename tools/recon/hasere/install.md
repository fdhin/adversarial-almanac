---
tool: "hasere"
build-system: "scripts"
upstream: "https://github.com/galkan/hasere"
recipe: "blackarch"
source: "https://github.com/galkan/hasere"
license: "custom"
blackarch-pkgver: "1.0"
---

# Installing hasere

> Discover the vhosts using google and bing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hasere) (version `1.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S hasere    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/hasere) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[hasere]] · [[BlackArch Tools]]

