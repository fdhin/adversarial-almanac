---
tool: "nullinux"
build-system: "scripts"
upstream: "https://github.com/m8r0wn/nullinux"
recipe: "blackarch"
source: "https://github.com/m8r0wn/nullinux"
license: "MIT"
blackarch-pkgver: "124.9f8727a"
---

# Installing nullinux

> Tool that can be used to enumerate OS information, domain information, shares, directories, and users through SMB null sessions.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nullinux) (version `124.9f8727a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S nullinux    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/m8r0wn/nullinux) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `ipparser`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[nullinux]] · [[BlackArch Tools]]

