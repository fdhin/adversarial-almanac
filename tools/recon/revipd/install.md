---
tool: "revipd"
build-system: "scripts"
upstream: "https://github.com/PypeRanger/revipd"
recipe: "blackarch"
source: "https://github.com/PypeRanger/revipd"
license: "custom:unknown"
blackarch-pkgver: "5.2aaacfb"
---

# Installing revipd

> A simple reverse IP domain scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/revipd) (version `5.2aaacfb`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S revipd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/PypeRanger/revipd) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[revipd]] · [[BlackArch Tools]]

