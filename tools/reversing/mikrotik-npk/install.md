---
tool: "mikrotik-npk"
build-system: "scripts"
upstream: "https://github.com/kost/mikrotik-npk"
recipe: "blackarch"
source: "https://github.com/kost/mikrotik-npk"
license: "custom:unknown"
blackarch-pkgver: "11.d54e97c"
---

# Installing mikrotik-npk

> Python tools for manipulating Mikrotik NPK format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mikrotik-npk) (version `11.d54e97c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mikrotik-npk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/kost/mikrotik-npk) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[mikrotik-npk]] · [[BlackArch Tools]]

