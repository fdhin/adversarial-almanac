---
tool: "badministration"
build-system: "scripts"
upstream: "https://github.com/ThunderGunExpress/BADministration"
recipe: "blackarch"
source: "https://github.com/ThunderGunExpress/BADministration"
license: "custom:unknonwn"
blackarch-pkgver: "16.69e4ec2"
---

# Installing badministration

> A tool which interfaces with management or administration applications from an offensive standpoint.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/badministration) (version `16.69e4ec2`, license custom:unknonwn).

## Arch / BlackArch

```bash
sudo pacman -S badministration    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ThunderGunExpress/BADministration.git
cd BADministration
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-click, python2-mcafee-epo, python2-orionsdk, python2-prettytable, python2-pyfiglet, python2-requests, python2-setuptools, python2-termcolor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[badministration]] · [[BlackArch Tools]]

