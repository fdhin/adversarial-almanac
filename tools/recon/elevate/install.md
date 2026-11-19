---
tool: "elevate"
build-system: "scripts"
upstream: "https://github.com/Healdb/Elevate"
recipe: "blackarch"
source: "https://github.com/Healdb/Elevate"
license: "custom:unkown"
blackarch-pkgver: "27.1272d51"
---

# Installing elevate

> Horizontal domain discovery tool you can use to discover other domains owned by a given company.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/elevate) (version `27.1272d51`, license custom:unkown).

## Arch / BlackArch

```bash
sudo pacman -S elevate    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Healdb/Elevate) (the recipe uses a versioned archive).

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

Backlinks: [[recon]] · [[elevate]] · [[BlackArch Tools]]

