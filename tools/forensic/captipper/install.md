---
tool: "captipper"
build-system: "scripts"
upstream: "http://www.omriher.com/2015/01/captipper-malicious-http-traffic.html"
recipe: "blackarch"
source: "http://www.omriher.com/2015/01/captipper-malicious-http-traffic.html"
license: "Apache"
blackarch-pkgver: "74.3fb2836"
---

# Installing captipper

> Malicious HTTP traffic explorer tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/captipper) (version `74.3fb2836`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S captipper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/omriher/CapTipper.git
cd CapTipper
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[captipper]] · [[BlackArch Tools]]

