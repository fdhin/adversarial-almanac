---
tool: "frida-extract"
build-system: "scripts"
upstream: "https://github.com/OALabs/frida-extract"
recipe: "blackarch"
source: "https://github.com/OALabs/frida-extract"
license: "custom:unknown"
blackarch-pkgver: "13.abb3f14"
---

# Installing frida-extract

> Frida.re based RunPE (and MapViewOfSection) extraction tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/frida-extract) (version `13.abb3f14`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S frida-extract    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/OALabs/frida-extract) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- ⚠️ Python 2 only deps: python2-argparse, python2-frida (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[frida-extract]] · [[BlackArch Tools]]

