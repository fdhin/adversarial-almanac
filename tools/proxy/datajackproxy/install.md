---
tool: "datajackproxy"
build-system: "scripts"
upstream: "https://github.com/nccgroup/DatajackProxy"
recipe: "blackarch"
source: "https://github.com/nccgroup/DatajackProxy"
license: "MIT"
blackarch-pkgver: "42.f75f3a3"
---

# Installing datajackproxy

> A proxy which allows you to intercept TLS traffic in native x86 applications across platform.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/datajackproxy) (version `42.f75f3a3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S datajackproxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/DatajackProxy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `frida`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[datajackproxy]] · [[BlackArch Tools]]

