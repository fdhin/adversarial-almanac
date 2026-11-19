---
tool: "tpcat"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/tpcat/"
recipe: "blackarch"
source: "http://downloads.sourceforge.net/tpcat/TPCAT.py"
license: "GPL"
blackarch-pkgver: "latest"
---

# Installing tpcat

> Tool based upon pcapdiff by the EFF.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tpcat) (version `latest`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tpcat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO http://downloads.sourceforge.net/tpcat/tpcat.zip
tar xf tpcat.zip
cd tpcat
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `wxpython`
- ⚠️ Python 2 only deps: python2-pcapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[tpcat]] · [[BlackArch Tools]]

