---
tool: "malcom"
build-system: "scripts"
upstream: "https://github.com/tomchop/malcom"
recipe: "blackarch"
source: "https://github.com/tomchop/malcom"
license: "custom:unknown"
blackarch-pkgver: "708.02e55b9"
---

# Installing malcom

> Analyze a system's network communication using graphical representations of network traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/malcom) (version `708.02e55b9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S malcom    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tomchop/malcom) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pip, python2-typing, python2-virtualenv (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[malcom]] · [[BlackArch Tools]]

