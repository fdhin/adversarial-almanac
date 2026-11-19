---
tool: "ciphertest"
build-system: "scripts"
upstream: "https://github.com/OpenSecurityResearch/ciphertest"
recipe: "blackarch"
source: "https://github.com/OpenSecurityResearch/ciphertest"
license: "custom:unknown"
blackarch-pkgver: "22.e33eb4a"
---

# Installing ciphertest

> A better SSL cipher checker using gnutls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ciphertest) (version `22.e33eb4a`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ciphertest    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/OpenSecurityResearch/ciphertest) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[ciphertest]] · [[BlackArch Tools]]

