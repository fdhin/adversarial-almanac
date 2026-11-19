---
tool: "halcyon"
build-system: "scripts"
upstream: "https://www.blackhatlibrary.net/Halcyon"
recipe: "blackarch"
aur-package: "halcyon"
source: "https://www.blackhatlibrary.net/Halcyon"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.0"
---

# Installing halcyon

> A repository crawler that runs checksums for static files found within a given git repository.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/halcyon) (version `1.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S halcyon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.blackhatlibrary.net/Halcyon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[halcyon]] · [[BlackArch Tools]]

