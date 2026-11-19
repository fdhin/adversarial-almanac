---
tool: "fridump"
build-system: "scripts"
upstream: "https://github.com/Nightbringer21/fridump"
recipe: "blackarch"
source: "https://github.com/Nightbringer21/fridump"
license: "custom:unknown"
blackarch-pkgver: "23.3e64ee0"
---

# Installing fridump

> A universal memory dumper using Frida.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fridump) (version `23.3e64ee0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S fridump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Nightbringer21/fridump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-frida (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[fridump]] · [[BlackArch Tools]]

