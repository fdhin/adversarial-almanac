---
tool: "wikigen"
build-system: "scripts"
upstream: "https://github.com/zombiesam/wikigen"
recipe: "blackarch"
source: "https://github.com/zombiesam/wikigen"
license: "WTFPL"
blackarch-pkgver: "8.348aa99"
---

# Installing wikigen

> A script to generate wordlists out of wikipedia pages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wikigen) (version `8.348aa99`, license WTFPL).

## Arch / BlackArch

```bash
sudo pacman -S wikigen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/zombiesam/wikigen) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[wikigen]] · [[BlackArch Tools]]

