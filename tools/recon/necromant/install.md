---
tool: "necromant"
build-system: "scripts"
upstream: "https://github.com/PentesterES/Necromant"
recipe: "blackarch"
source: "https://github.com/PentesterES/Necromant"
license: "custom:unknown"
blackarch-pkgver: "4.53930c2"
---

# Installing necromant

> Python Script that search unused Virtual Hosts in Web Servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/necromant) (version `4.53930c2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S necromant    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PentesterES/Necromant.git
cd Necromant
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[necromant]] · [[BlackArch Tools]]

