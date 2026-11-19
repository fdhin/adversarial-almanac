---
tool: "goddi"
build-system: "scripts"
upstream: "https://github.com/NetSPI/goddi"
recipe: "blackarch"
source: "https://github.com/NetSPI/goddi"
license: "BSD"
blackarch-pkgver: "1.2"
---

# Installing goddi

> Dumps Active Directory domain information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/goddi) (version `1.2`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S goddi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NetSPI/goddi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[goddi]] · [[BlackArch Tools]]

