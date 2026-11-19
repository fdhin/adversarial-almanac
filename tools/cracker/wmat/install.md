---
tool: "wmat"
build-system: "scripts"
upstream: "http://netsec.rs/70/tools.html"
recipe: "blackarch"
source: "http://security-net.biz/wmat/wmat.zip"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing wmat

> Automatic tool for testing webmail accounts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wmat) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wmat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO http://security-net.biz/wmat/wmat.zip
tar xf wmat.zip
cd wmat
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`
- ⚠️ Python 2 only deps: python2-pycurl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[wmat]] · [[BlackArch Tools]]

