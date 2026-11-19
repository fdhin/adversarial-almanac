---
tool: "ircsnapshot"
build-system: "scripts"
upstream: "https://github.com/bwall/ircsnapshot"
recipe: "blackarch"
source: "https://github.com/bwall/ircsnapshot"
license: "MIT"
blackarch-pkgver: "94.cb02a85"
---

# Installing ircsnapshot

> Tool to gather information from IRC servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ircsnapshot) (version `94.cb02a85`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ircsnapshot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bwall/ircsnapshot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ircsnapshot]] · [[BlackArch Tools]]

