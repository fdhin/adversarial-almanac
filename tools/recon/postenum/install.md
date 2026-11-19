---
tool: "postenum"
build-system: "scripts"
upstream: "https://github.com/mbahadou/postenum"
recipe: "blackarch"
source: "https://github.com/mbahadou/postenum"
license: "MIT"
blackarch-pkgver: "126.f91ba1c"
---

# Installing postenum

> Clean, nice and easy tool for basic/advanced privilege escalation techniques.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/postenum) (version `126.f91ba1c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S postenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mbahadou/postenum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[postenum]] · [[BlackArch Tools]]

