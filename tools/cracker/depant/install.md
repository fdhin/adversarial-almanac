---
tool: "depant"
build-system: "scripts"
upstream: "http://midnightresearch.com/projects/depant/"
recipe: "blackarch"
source: "http://midnightresearch.com/projects/depant/"
license: "GPL"
blackarch-pkgver: "0.3a"
---

# Installing depant

> Check network for services with default passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/depant) (version `0.3a`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S depant    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://midnightresearch.com/projects/depant/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `hydra`
  - `nmap`
- ⚠️ Python 2 only deps: python2-ipcalc, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[depant]] · [[BlackArch Tools]]

