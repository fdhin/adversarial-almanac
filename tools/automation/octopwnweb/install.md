---
tool: "octopwnweb"
build-system: "scripts"
upstream: "https://github.com/skelsec/octopwnweb"
recipe: "blackarch"
source: "https://github.com/skelsec/octopwnweb"
license: "custom:unknown"
blackarch-pkgver: "14.e0f83ee"
---

# Installing octopwnweb

> Internal pentest framework running in your browser via WebAssembly, powerd by Pyodide

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/octopwnweb) (version `14.e0f83ee`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S octopwnweb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/skelsec/octopwnweb) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `wsnet`
- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[octopwnweb]] · [[BlackArch Tools]]

