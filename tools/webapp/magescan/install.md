---
tool: "magescan"
build-system: "scripts"
upstream: "https://github.com/steverobbins/magescan"
recipe: "blackarch"
source: "https://github.com/steverobbins/magescan"
license: "custom:unknown"
blackarch-pkgver: "1.12.9"
---

# Installing magescan

> Scan a Magento site for information.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/magescan) (version `1.12.9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S magescan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/steverobbins/magescan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[magescan]] · [[BlackArch Tools]]

