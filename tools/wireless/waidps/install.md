---
tool: "waidps"
build-system: "scripts"
upstream: "https://github.com/SYWorks/waidps"
recipe: "blackarch"
source: "https://github.com/SYWorks/waidps.git"
license: "GPL3"
blackarch-pkgver: "16.ff8d270"
---

# Installing waidps

> Wireless Auditing, Intrusion Detection & Prevention System.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/waidps) (version `16.ff8d270`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S waidps    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/SYWorks/waidps.git
cd waidps
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[waidps]] · [[BlackArch Tools]]

