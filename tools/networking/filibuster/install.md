---
tool: "filibuster"
build-system: "scripts"
upstream: "https://github.com/subinacls/Filibuster"
recipe: "blackarch"
source: "https://github.com/subinacls/Filibuster"
license: "GPL3"
blackarch-pkgver: "167.c54ac80"
---

# Installing filibuster

> A Egress filter mapping application with additional functionality.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/filibuster) (version `167.c54ac80`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S filibuster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/subinacls/Filibuster.git
cd Filibuster
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-configparser, python2-dnspython, python2-numpy, python2-pycryptodome, python2-pyliblzma (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[filibuster]] · [[BlackArch Tools]]

