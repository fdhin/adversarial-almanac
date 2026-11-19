---
tool: "tplmap"
build-system: "scripts"
upstream: "https://github.com/epinna/tplmap"
recipe: "blackarch"
source: "https://github.com/epinna/tplmap"
license: "custom:unknown"
blackarch-pkgver: "719.616b0e5"
---

# Installing tplmap

> Automatic Server-Side Template Injection Detection and Exploitation Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tplmap) (version `719.616b0e5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tplmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/epinna/tplmap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-certifi, python2-chardet, python2-flask, python2-idna, python2-requests, python2-urllib3, python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[tplmap]] · [[BlackArch Tools]]

