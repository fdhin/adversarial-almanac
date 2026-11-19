---
tool: "fstealer"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/106450/FStealer-Filesystem-Mirroring-Tool.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/utilities/fstealer.tar.gz"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing fstealer

> Automates file system mirroring through remote file disclosure vulnerabilities on Linux machines.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fstealer) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S fstealer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/utilities/fstealer.tar.gz
tar xf fstealer.tar.gz
cd fstealer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[fstealer]] · [[BlackArch Tools]]

