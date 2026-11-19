---
tool: "routerhunter"
build-system: "scripts"
upstream: "https://github.com/jh00nbr/Routerhunter-2.0"
recipe: "blackarch"
source: "https://github.com/jh00nbr/Routerhunter-2.0"
license: "custom:unknown"
blackarch-pkgver: "21.4da257c"
---

# Installing routerhunter

> Tool used to find vulnerable routers and devices on the Internet and perform tests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/routerhunter) (version `21.4da257c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S routerhunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/jh00nbr/Routerhunter-2.0.git
cd Routerhunter-2.0
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[routerhunter]] · [[BlackArch Tools]]

