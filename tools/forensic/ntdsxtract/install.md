---
tool: "ntdsxtract"
build-system: "scripts"
upstream: "https://github.com/csababarta/ntdsxtract"
recipe: "blackarch"
source: "https://github.com/csababarta/ntdsxtract"
license: "GPL3"
blackarch-pkgver: "34.7fa1c8c"
---

# Installing ntdsxtract

> Active Directory forensic framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ntdsxtract) (version `34.7fa1c8c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ntdsxtract    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/csababarta/ntdsxtract) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[ntdsxtract]] · [[BlackArch Tools]]

