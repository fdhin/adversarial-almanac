---
tool: "networkmap"
build-system: "scripts"
upstream: "https://github.com/lorenzog/NetworkMap"
recipe: "blackarch"
source: "https://github.com/lorenzog/NetworkMap"
license: "GPL3"
blackarch-pkgver: "58.f5faf17"
---

# Installing networkmap

> Post-exploitation network mapper.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/networkmap) (version `58.f5faf17`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S networkmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lorenzog/NetworkMap.git
cd NetworkMap
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-decorator, python2-ipaddress, python2-networkx, python2-nose, python2-pydotplus, python2-pyparsing (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[networkmap]] · [[BlackArch Tools]]

