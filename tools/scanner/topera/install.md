---
tool: "topera"
build-system: "scripts"
upstream: "https://github.com/toperaproject/topera"
recipe: "blackarch"
source: "https://github.com/toperaproject/topera"
license: "GPL2"
blackarch-pkgver: "19.3e230fd"
---

# Installing topera

> An IPv6 security analysis toolkit, with the particularity that their attacks can't be detected by Snort.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/topera) (version `19.3e230fd`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S topera    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/toperaproject/topera) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[topera]] · [[BlackArch Tools]]

