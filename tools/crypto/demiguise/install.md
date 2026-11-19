---
tool: "demiguise"
build-system: "scripts"
upstream: "https://github.com/nccgroup/demiguise"
recipe: "blackarch"
source: "https://github.com/nccgroup/demiguise"
license: "AGPL"
blackarch-pkgver: "11.58d5681"
---

# Installing demiguise

> HTA encryption tool for RedTeams.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/demiguise) (version `11.58d5681`, license AGPL).

## Arch / BlackArch

```bash
sudo pacman -S demiguise    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/demiguise) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[demiguise]] · [[BlackArch Tools]]

