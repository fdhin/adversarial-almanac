---
tool: "dnsbf"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/dnsbf/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/dnsbf/"
license: "BSD-3-Clause"
blackarch-pkgver: "0.3"
---

# Installing dnsbf

> Search for available domain names in an IP range.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsbf) (version `0.3`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S dnsbf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/dnsbf/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[dnsbf]] · [[BlackArch Tools]]

