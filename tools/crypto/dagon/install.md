---
tool: "dagon"
build-system: "scripts"
upstream: "https://github.com/Ekultek/Dagon"
recipe: "blackarch"
source: "https://github.com/Ekultek/Dagon"
license: "custom:unknown"
blackarch-pkgver: "244.f065d7b"
---

# Installing dagon

> Advanced Hash Manipulation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dagon) (version `244.f065d7b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dagon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Ekultek/Dagon.git
cd Dagon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-bcrypt, python2-colorlog, python2-passlib, python2-pydes, python2-pysha3, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[dagon]] · [[BlackArch Tools]]

