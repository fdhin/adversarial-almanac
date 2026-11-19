---
tool: "quickrecon"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/104314/QuickRecon-0.3.2.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/104314/QuickRecon-0.3.2.html"
license: "GPL3"
blackarch-pkgver: "0.3.2"
---

# Installing quickrecon

> A python script for simple information gathering. It attempts to find subdomain names, perform zone transfers and gathers emails from Google and Bing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/quickrecon) (version `0.3.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S quickrecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/104314/QuickRecon.3.2.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `pyqt4-common`
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-html5lib, python2-httplib2, python2-pyqt4, python2-sip (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[quickrecon]] · [[BlackArch Tools]]

