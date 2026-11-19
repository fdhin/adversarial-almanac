---
tool: "credmap"
build-system: "scripts"
upstream: "https://github.com/lightos/credmap"
recipe: "blackarch"
source: "https://github.com/lightos/credmap"
license: "custom:unknown"
blackarch-pkgver: "116.d862247"
---

# Installing credmap

> The Credential mapper - Tool that was created to bring awareness to the dangers of credential reuse.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/credmap) (version `116.d862247`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S credmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lightos/credmap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[credmap]] · [[BlackArch Tools]]

