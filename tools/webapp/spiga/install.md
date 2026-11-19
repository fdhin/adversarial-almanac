---
tool: "spiga"
build-system: "scripts"
upstream: "https://github.com/getdual/scripts-n-tools/blob/master/spiga.py"
recipe: "blackarch"
source: "https://github.com/getdual/scripts-n-tools/blob/master/spiga.py"
license: "custom:unknown"
blackarch-pkgver: "690.f1831cd"
---

# Installing spiga

> Configurable web resource scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spiga) (version `690.f1831cd`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S spiga    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/WSTNPHX/scripts-n-tools.git
cd scripts-n-tools
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[spiga]] · [[BlackArch Tools]]

