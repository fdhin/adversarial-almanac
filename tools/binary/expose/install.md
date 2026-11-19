---
tool: "expose"
build-system: "node"
upstream: "https://github.com/ExpoSEJS/ExpoSE"
recipe: "blackarch"
aur-package: "expose"
source: "https://github.com/ExpoSEJS/ExpoSE.git"
license: "MIT"
blackarch-pkgver: "1144.218d915"
---

# Installing expose

> A Dynamic Symbolic Execution (DSE) engine for JavaScript

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/expose) (version `1144.218d915`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S expose    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

```bash
git clone https://github.com/ExpoSEJS/ExpoSE.git
cd ExpoSE
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `clang`
  - `gnuplot`
  - `libxtst`
  - `mitmproxy`
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[expose]] · [[BlackArch Tools]]

