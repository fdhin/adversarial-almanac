---
tool: "ubiquiti-probing"
build-system: "scripts"
upstream: "https://github.com/headlesszeke/ubiquiti-probing"
recipe: "blackarch"
source: "https://github.com/headlesszeke/ubiquiti-probing"
license: "custom:unknown"
blackarch-pkgver: "5.c28f4c1"
---

# Installing ubiquiti-probing

> A Ubiquiti device discovery tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ubiquiti-probing) (version `5.c28f4c1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ubiquiti-probing    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/headlesszeke/ubiquiti-probing) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ruby`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[ubiquiti-probing]] · [[BlackArch Tools]]

