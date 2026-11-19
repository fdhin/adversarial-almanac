---
tool: "bdfproxy"
build-system: "scripts"
upstream: "https://github.com/secretsquirrel/BDFProxy"
recipe: "blackarch"
source: "https://github.com/secretsquirrel/BDFProxy"
license: "GPL3"
blackarch-pkgver: "107.276c367"
---

# Installing bdfproxy

> Patch Binaries via MITM: BackdoorFactory + mitmProxy

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bdfproxy) (version `107.276c367`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bdfproxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/secretsquirrel/BDFProxy.git
cd BDFProxy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `backdoor-factory`
  - `capstone`
  - `mitmproxy`
  - `python2`
- ⚠️ Python 2 only deps: python2-configobj, python2-pefile, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[bdfproxy]] · [[BlackArch Tools]]

