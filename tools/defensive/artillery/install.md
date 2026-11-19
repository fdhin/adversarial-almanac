---
tool: "artillery"
build-system: "scripts"
upstream: "https://www.trustedsec.com/downloads/artillery/"
recipe: "blackarch"
source: "https://www.trustedsec.com/downloads/artillery/"
license: "BSD-3-Clause"
blackarch-pkgver: "1.5.1.r218.g805a5d8"
---

# Installing artillery

> Blue team tool designed to protect Linux and Windows operating systems through multiple methods.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/artillery) (version `1.5.1.r218.g805a5d8`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S artillery    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.trustedsec.com/downloads/artillery/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/artillery`

Backlinks: [[defensive]] · [[artillery]] · [[BlackArch Tools]]

