---
tool: "volatility-extra"
build-system: "scripts"
upstream: "https://github.com/volatilityfoundation/community"
recipe: "blackarch"
source: "https://github.com/volatilityfoundation/community"
license: "custom:unknown"
blackarch-pkgver: "92.d9fc072"
---

# Installing volatility-extra

> Volatility plugins developed and maintained by the community.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/volatility-extra) (version `92.d9fc072`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S volatility-extra    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/volatilityfoundation/community.git
cd community
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `volatility3`
- ⚠️ Python 2 only deps: python2-pefile, python2-yara (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[volatility-extra]] · [[BlackArch Tools]]

