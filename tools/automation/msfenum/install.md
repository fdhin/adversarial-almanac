---
tool: "msfenum"
build-system: "scripts"
upstream: "https://github.com/wez3/msfenum"
recipe: "blackarch"
source: "https://github.com/wez3/msfenum"
license: "custom:unknown"
blackarch-pkgver: "36.6c6b77e"
---

# Installing msfenum

> A Metasploit auto auxiliary script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/msfenum) (version `36.6c6b77e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S msfenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/wez3/msfenum) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[msfenum]] · [[BlackArch Tools]]

