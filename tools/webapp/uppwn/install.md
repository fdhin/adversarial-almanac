---
tool: "uppwn"
build-system: "node"
upstream: "https://github.com/ferrery1/UpPwn"
recipe: "blackarch"
source: "https://github.com/ferrery1/UpPwn.git"
license: "custom:unknown"
blackarch-pkgver: "9.f69dec4"
---

# Installing uppwn

> A script that automates detection of security flaws on websites' file upload systems'.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uppwn) (version `9.f69dec4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S uppwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/ferrery1/UpPwn.git
cd UpPwn
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`
- **System libraries** (Arch name — translate for your distro):
  - `phantomjs`
  - `python2`
  - `xorg-server-xvfb`
- ⚠️ Python 2 only deps: python2-pymiproxy, python2-selenium, python2-xvfbwrapper (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[uppwn]] · [[BlackArch Tools]]

