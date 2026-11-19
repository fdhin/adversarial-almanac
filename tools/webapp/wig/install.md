---
tool: "wig"
build-system: "scripts"
upstream: "https://github.com/jekyc/wig"
recipe: "blackarch"
source: "https://github.com/jekyc/wig"
license: "custom:BSD"
blackarch-pkgver: "574.d5ddd91"
---

# Installing wig

> WebApp Information Gatherer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wig) (version `574.d5ddd91`, license custom:BSD).

## Arch / BlackArch

```bash
sudo pacman -S wig    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/jekyc/wig) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wig]] · [[BlackArch Tools]]

