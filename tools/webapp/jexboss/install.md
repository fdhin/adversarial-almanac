---
tool: "jexboss"
build-system: "scripts"
upstream: "https://github.com/joaomatosf/jexboss"
recipe: "blackarch"
source: "https://github.com/joaomatosf/jexboss"
license: "Apache"
blackarch-pkgver: "86.338b531"
---

# Installing jexboss

> Jboss verify and Exploitation Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jexboss) (version `86.338b531`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S jexboss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/joaomatosf/jexboss) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipaddress, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jexboss]] · [[BlackArch Tools]]

