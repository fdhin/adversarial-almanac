---
tool: "reelphish"
build-system: "scripts"
upstream: "https://github.com/fireeye/ReelPhish"
recipe: "blackarch"
source: "https://github.com/fireeye/ReelPhish"
license: "GPL3"
blackarch-pkgver: "5.dc1be33"
---

# Installing reelphish

> A Real-Time Two-Factor Phishing Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/reelphish) (version `5.dc1be33`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S reelphish    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/fireeye/ReelPhish.git
cd ReelPhish
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-selenium (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[reelphish]] · [[BlackArch Tools]]

