---
tool: "gopherus"
build-system: "scripts"
upstream: "https://github.com/tarunkant/Gopherus"
recipe: "blackarch"
aur-package: "gopherus"
source: "https://github.com/tarunkant/Gopherus"
license: "MIT"
blackarch-pkgver: "33.90a2fd5"
---

# Installing gopherus

> Tool generates gopher link for exploiting SSRF and gaining RCE in various servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gopherus) (version `33.90a2fd5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gopherus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tarunkant/Gopherus.git
cd Gopherus
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[gopherus]] · [[BlackArch Tools]]

