---
tool: "neglected"
build-system: "scripts"
upstream: "https://github.com/GuerrillaWarfare/neglected"
recipe: "blackarch"
source: "https://github.com/GuerrillaWarfare/neglected"
license: "custom:unknown"
blackarch-pkgver: "8.68d02b3"
---

# Installing neglected

> Facebook CDN Photo Resolver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/neglected) (version `8.68d02b3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S neglected    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GuerrillaWarfare/neglected) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[neglected]] · [[BlackArch Tools]]

