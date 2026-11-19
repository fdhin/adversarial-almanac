---
tool: "rpdscan"
build-system: "scripts"
upstream: "https://github.com/freakyclown/RPDscan"
recipe: "blackarch"
source: "https://github.com/freakyclown/RPDscan"
license: "custom:unknown"
blackarch-pkgver: "2.a71b0f3"
---

# Installing rpdscan

> Remmina Password Decoder and scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rpdscan) (version `2.a71b0f3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rpdscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/freakyclown/RPDscan.git
cd RPDscan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rpdscan]] · [[BlackArch Tools]]

