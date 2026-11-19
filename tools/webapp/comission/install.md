---
tool: "comission"
build-system: "scripts"
upstream: "https://github.com/Intrinsec/comission"
recipe: "blackarch"
source: "https://github.com/Intrinsec/comission"
license: "MIT"
blackarch-pkgver: "v1.2.0.r14.g67b890e"
---

# Installing comission

> WhiteBox CMS analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/comission) (version `v1.2.0.r14.g67b890e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S comission    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Intrinsec/comission) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `checksumdir`, `lxml`, `openpyxl`, `requests`, `xlsxwriter`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[comission]] · [[BlackArch Tools]]

