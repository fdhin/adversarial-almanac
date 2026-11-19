---
tool: "nsdtool"
build-system: "scripts"
upstream: "http://www.curesec.com/en/publications/tools.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/utilities/nsdtool.tar.bz2"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing nsdtool

> A netgear switch discovery tool. It contains some extra features like bruteoforce and setting a new password.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nsdtool) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S nsdtool    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/utilities/nsdtool.tar.bz2
tar xf nsdtool.tar.bz2
cd nsdtool
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `python2`
  - `python3`
- ⚠️ Python 2 only deps: python2-configparser (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[nsdtool]] · [[BlackArch Tools]]

