---
tool: "xfltreat"
build-system: "scripts"
upstream: "https://github.com/earthquake/xfltreat/"
recipe: "blackarch"
source: "https://github.com/earthquake/xfltreat.git"
license: "custom:unknown"
blackarch-pkgver: "270.17d4ec8"
---

# Installing xfltreat

> Tunnelling framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xfltreat) (version `270.17d4ec8`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xfltreat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/earthquake/xfltreat.git
cd xfltreat
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pyroute2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[xfltreat]] · [[BlackArch Tools]]

